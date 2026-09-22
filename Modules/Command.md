# Command

> Run custom shell scripts

| | |
|---|---|
| Module type | `command` |
| Default order | — (no `defaultOrder`; `--gen-config` emits it in a fallback group) |
| Module source | `src/modules/command/command.c` |
| Detection source | `src/detection/command/` |

Runs a shell command and prints its output as a module value. With a key:

```
Command: 24°C
```

or, with `"splitLines": true`, one line per output line, numbered:

```
Command 1: a
Command 2: b
Command 3: c
```

This is the most flexible module in fastfetch: it can produce anything a shell one-liner can. It is
also the module with the most sharp edges — read [Pitfalls](#pitfalls) before using it.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux / FreeBSD / NetBSD / OpenBSD / DragonFly / MidnightBSD / Solaris / Haiku / GNU | `src/detection/command/command.c` | Shared source, no `#ifdef` branches |
| macOS | Same | |
| Android | Same | |
| Windows | Same | Defaults differ: `shell` is `cmd.exe`, `param` is `/c` |

The detection source is listed in the platform-independent part of `CMakeLists.txt`, so it is
compiled everywhere. Process handling itself lives in
`src/common/impl/processing_{unix,windows}.c`.

## Configuration

| Option | Type | Default | Description |
|---|---|---|---|
| `shell` | string | `cmd.exe` (Windows) / `/bin/sh` (else) | Interpreter used to run `text`. |
| `param` | string | `/c` (Windows) / `-c` (else) | Argument passed before `text`. **If set to an empty string it is omitted entirely**, so the argv becomes `{ shell, text }`. |
| `text` | string | `""` | The command to run. Empty means the module always fails with `No command text specified`. |
| `useStdErr` | boolean | `false` | Read the child's stderr instead of its stdout. |
| `parallel` | boolean | `true` | Spawn the process before other modules run and read it later. See below. |
| `splitLines` | boolean | `false` | Print one module line per line of output. |
| `key` | string | (module name) | Key text; `" "` hides it. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyIcon` | string | built-in glyph | The icon printed when `display.key.type` includes the icon bit. Set it to any glyph you like, or to `""` to print none. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Output format string; only `{result}`. |
| `condition` | object | – | See [Configuration](Modules/Global#condition-in-practice). |

About `parallel`:

- `parallel: true` (the default) spawns the process during the **prepare** pass, before any module is
  printed, and reads it when the module is reached. Several `command` modules therefore run
  concurrently and the total wall time is close to the slowest one.
- `parallel: false` spawns and reads the process at print time. Use it when the command depends on
  the ordering of the other modules, or when you want its side effects — writing a file, hitting an
  API — to happen only if the module is actually printed.

About `text`: because a shell is involved, quoting is the shell's business. `"text": "echo \"a b\""`
runs `echo "a b"`. If you set `shell` to a program that does not take a `-c`-style parameter, set
`param` to `""`.

## Format string

Run `fastfetch -h command-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{result}` | The command's output, with trailing whitespace already trimmed |

With `splitLines` enabled, `{result}` holds the *current* line, and the format is evaluated once per
line.

`format` can also hold any literal text, which makes it a cheaper alternative to `custom` when the
text you want to print happens to come from a command.

## JSON output

Without `splitLines`, `result` is a string:

```jsonc
{ "type": "Command", "result": "24°C" }
```

With `splitLines`, it is an array of strings:

```jsonc
{ "type": "Command", "result": ["a", "b", "c"] }
```

Failures are reported as an `error` member, and the entry has no `result`:

```jsonc
{ "type": "Command", "error": "No result generated" }
```

## Examples

The current weather from `wttr.in`, split into lines:

```jsonc
{
  "modules": [
    { "type": "command", "text": "curl -s 'wttr.in/?format=%l:+%c+%t'", "key": "Weather", "parallel": true }
  ]
}
```

Number of pending package updates (Debian and derivatives):

```jsonc
{ "type": "command", "text": "apt list --upgradable 2>/dev/null | tail -n +2 | wc -l", "key": "Updates" }
```

Show the current git branch, but only inside a repository:

```jsonc
{
  "type": "command",
  "text": "git rev-parse --abbrev-ref HEAD",
  "key": "Branch",
  "useStdErr": false
}
```

Reading stderr instead of stdout — useful for tools that log to stderr:

```jsonc
{ "type": "command", "text": "my-tool --version", "useStdErr": true }
```

A command that does not use a shell interpreter at all — `param: ""` makes the argv
`{ shell, text }`, so `text` is a single argument:

```jsonc
{ "type": "command", "shell": "/usr/bin/uname", "param": "", "text": "-r" }
```

```
Command: 6.12.4
```

## Pitfalls

- **`command` is refused under `--structure`.** The prepare hook is only wired into the JSON-config
  path, so a `-s Command` module would have nothing to collect. Rather than print an internal
  marker, fastfetch rejects it up front and exits with status 481:

  ```
  Error: module `command` is not supported with `--structure`
         Its options can only be read from the JSON config, which `--structure` bypasses.
         Add it to the config instead, e.g. `{ "modules": [ { "type": "command", "text": "uname -r" } ] }`
  ```

  Add the module to the config instead of passing it to `-s`.
- **`splitLines` only splits on `\n`.** On Windows a trailing `\r` is stripped, but on Unix it is
  kept, so CRLF output (a file with CRLF endings read on Linux, for instance) shows up as
  `Command 1: x^M` on the console and as `"x\r"` in the JSON array. Strip it in the command itself
  (`tr -d '\r'`, `dos2unix`, …) if you need it gone.
- **Empty output is an error, not an empty value.** A command that succeeds but prints nothing
  produces `No result generated` and, with `display.showErrors` off (the default), a blank line.
  Silent failure is easy to mistake for "no data".
- **Trailing whitespace is trimmed**, but leading whitespace is not. A multi-line command that
  emits a blank first line will show it.
- **The command runs in `fastfetch`'s environment and working directory**, and inherits stdin. With
  `--dynamic-interval` the command is re-run on every refresh, which is usually what you want and
  occasionally a surprise.
- **`parallel` is not a scheduler.** Ordering between two `parallel` commands is not defined, even
  though their *outputs* are printed in config order.
- **With `param: ""` you can pass exactly one argument.** The argv is `{ shell, text }`, so anything
  needing flags plus operands must go through the shell (`"/bin/sh"` + `param: "-c"`) or a wrapper
  script. Note the argument is passed verbatim — no splitting on spaces.
- **Module options are JSON-only.** `--command-text "…"` and friends were removed in 2.52.0.

## Implementation

The module is split across two passes, and that split is the source of most of its quirks.

**Prepare pass** — `ffPrepareCommand()` (`src/detection/command/command.c:29`) is called from
`prepareModuleJsonObject()` (`src/common/impl/jsonconfig.c`) for every `command` entry in the
config, before anything is printed. When `parallel` is true it spawns the process and appends an
`FFCommandResultBundle` (handle + error) to the file-static `commandQueue`. The queue is a plain
FIFO with no identity attached:

```c
typedef struct FFCommandResultBundle {
    FFProcessHandle handle;
    const char* error;
} FFCommandResultBundle;

static FFlist commandQueue; // FIFO, non-thread-safe
```

**Print pass** — `ffPrintCommand()` calls `ffDetectCommand()`, which pops **one** bundle from the
front of the queue with `FF_LIST_SHIFT` and reads its output with `ffProcessReadOutput()`. Nothing
checks that the popped bundle belongs to the module being printed: the pairing is purely positional,
which is why a module-set mismatch between the two passes corrupts the output rather than failing.

`spawnProcess()` builds the argv:

- `param` non-empty → `{ shell, param, text, nullptr }`
- `param` empty → `{ shell, text, nullptr }`

and calls `ffProcessSpawn(argv, useStdErr, FF_PROCESS_INHERIT_STDIN, &handle)`.
`FF_PROCESS_INHERIT_STDIN` (`src/common/processing.h:25`) is deliberate: detaching stdin breaks
`/system/bin/cmd` on Android, which forwards it over binder.

`ffDetectCommand()` then trims trailing whitespace with `ffStrbufTrimRightSpace()` and reports
`nullptr` for success.

**Printing.** With `splitLines`, the result is walked with `ffStrbufGetline()`
(`src/common/FFstrbuf.h:139`, a `\n`-delimited `getdelim` that *modifies the buffer in place*,
replacing the delimiter with `'\0'`) and each line goes through `ffPrintLogoAndKey()` with an
incrementing `uint32_t` index. Without it, the whole buffer is written at once with index `0`.

The JSON result is produced by a second, independent call to `ffDetectCommand()` — the JSON path
never prints, so the two paths do not interfere. Note that `--format json` therefore also *runs* the
command.
