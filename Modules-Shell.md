# Shell

> Print the current shell name and version

| | |
|---|---|
| Module type | `shell` |
| Default order | 15 (only used by `--gen-config`) |
| Module source | `src/modules/shell/shell.c` |
| Detection source | `src/detection/terminalshell/` (shared with the `Terminal` module) |

Prints the pretty name of the shell fastfetch was started from, with its version when one could be
determined.

```
Shell: zsh 5.9.1
Shell: bash 5.2.37
Shell: PowerShell 7.4.6
```

The name is the *pretty name*, not the process name: `pwsh` prints as `PowerShell`, `nu` as
`nushell`, `oil.ovm` as `Oils` and `busybox` as `ash`. Everything else prints the executable's
basename.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `terminalshell_linux.c` | Walks `/proc/<pid>/stat` up the parent chain |
| Android | `terminalshell_linux.c` | Same reader |
| FreeBSD / NetBSD / OpenBSD / DragonFly | `terminalshell_linux.c` | Same file; the shared helper uses `sysctl` on these |
| Solaris / illumos | `terminalshell_linux.c` | Same file |
| macOS | `terminalshell_linux.c` | Same file; the helper uses `KERN_PROC_PID` |
| Haiku | `terminalshell_linux.c` | Same file |
| GNU/Hurd | `terminalshell_linux.c` | Same file |
| Windows | `terminalshell_windows.c` | Walks the NT process table |

Despite the `_linux` suffix, `terminalshell_linux.c` is the implementation for every Unix-like
platform. It calls `ffProcessGetBasicInfoLinux()` / `ffProcessGetInfoLinux()`, which are equally
mis-named: `src/common/impl/processing_linux.c` carries `#elif defined(__APPLE__)`,
`__FreeBSD__`, `__NetBSD__`, `__OpenBSD__` … branches for the same two functions.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works, `""` prints none. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

There are no module-specific keys. One environment variable changes the detection itself:

| Variable | Effect |
|---|---|
| `FFTS_IGNORE_PARENT=1` | Skip one extra level of the parent chain before looking for the shell. Useful when fastfetch is started through a wrapper that would otherwise be mistaken for the shell. |

Version detection is gated on the global `general.detectVersion` setting.

## Format string

Run `fastfetch -h shell-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{process-name}` | Shell process name, as the kernel reports it |
| `{exe}` | First argument of the shell's command line (`arg0` on Unix) |
| `{exe-name}` | Basename of `{exe}` |
| `{version}` | Shell version, empty when it could not be determined |
| `{pid}` | Shell process ID |
| `{pretty-name}` | The mapped display name (what the default output prints) |
| `{exe-path}` | Full path of the shell executable, resolved through `/proc/<pid>/exe` on Linux |
| `{tty}` | Numeric identifier of the controlling terminal, or `-1` |

## JSON output

```jsonc
{
    "type": "Shell",
    "result": {
        "exe": "/usr/bin/zsh",
        "exeName": "zsh",
        "exePath": "/usr/bin/zsh",
        "pid": 21834,
        "ppid": 11447,
        "processName": "zsh",
        "prettyName": "zsh",
        "version": "5.9.1",
        "tty": 2
    }
}
```

`tty` is a **number** in the Shell result (or `null` when there is no controlling terminal). The
`Terminal` module's `tty` is a path string instead — the two are not interchangeable. `ppid` has
no format variable, so it is only reachable through JSON.

## Examples

```jsonc
// Identify the shell precisely, including where it lives
{ "type": "shell", "format": "{pretty-name} {version} ({exe-path})" }
```

```jsonc
{ "type": "shell", "key": "SHELL", "format": "{process-name} pid {pid}" }
```

## Pitfalls

- **A non-shell parent is mistaken for the shell.** The detector walks up from fastfetch's parent
  and accepts the first process that is neither the login shell nor on a skip list. Run from
  Python, a build script or a wrapper, and the "shell" becomes that program. Reproduced by
  launching fastfetch from `python3 -c`:

  ```jsonc
  { "processName": "python3.13", "exe": "c", "exeName": "c", "prettyName": "c", "version": "" }
  ```

  `{exe}` is `-c` — Python's `argv[0]` — with its leading dash trimmed because login shells start
  with one. `FFTS_IGNORE_PARENT=1` skips one more level and is the only workaround.
- **`{version}` is empty for anything outside a fixed list.** Only `bash`, `zsh`, `fish`, `pwsh`,
  `csh`/`tcsh`, `nu`, `ksh`/`mksh`, `oksh`, `oil.ovm`, `elvish`, `ash`/`busybox`, `xonsh`, `brush`,
  and on Windows `powershell`/`powershell_ise`, have a version probe. Anything else — `dash`,
  `yash`, `rush`, a custom shell — reports the name with no version and no explanation.
- **Version detection executes the shell.** `bash`, `fish`, `pwsh`, `nu`, `csh`, `tcsh`, `elvish`,
  `ash`, `brush` and `xonsh` are run with `--version`; `ksh`/`mksh` with `--version` on stderr and
  then `-c 'echo $KSH_VERSION'`; `oksh` with `-c 'echo $OKSH_VERSION'`. `bash` first tries to
  extract the version string from the binary without executing it, and only falls back to running
  it. Set `general.detectVersion` to `false` for a side-effect-free run.
- **`sh` is never probed.** `fftsGetShellVersion()` returns early for `sh` (upstream issue #849),
  so `Shell: sh` is printed without a version even when `/bin/sh` is a full bash.
- **`pwsh` honours `$POWERSHELL_VERSION`.** If the variable is set it is used verbatim and the
  shell is not executed. Without it the module runs the shell, which the source calls
  "extremely slow".
- **`{tty}` is not a device path.** On Linux it is the low byte of the kernel's `tty_nr` field
  (`tty_ & 0xFF`, `processing_linux.c:530`); on macOS and the BSDs it is the numeric suffix of the
  `pts/N` device. `2` means "the terminal that `/dev/ttys002` / `/dev/pts/2` refers to", not a file
  descriptor, and values above 255 cannot be represented on Linux.
- **The process name is truncated to 16 characters on macOS.** `ffProcessGetBasicInfoLinux()` reads
  `p_comm` on Apple platforms and the source notes it is truncated to 16 chars, so a long shell
  name arrives shortened.
- **`xonsh` is not detected on macOS.** The source comment records that the kernel reports the
  process as `Python` there, so the `xonsh` branch is unreachable on Apple platforms.
- **Shell and Terminal are one detection.** `ffDetectTerminal()` starts from
  `ffDetectShell()->ppid`, and both results live in a process-wide cache
  (`FFcacheEntryShell` / `FFcacheEntryTerminal`). A wrong shell therefore also produces a wrong
  terminal, and under `--dynamic-interval` both are re-detected on every round.

## Implementation

### The parent-chain walk

`ffDetectShell()` (`terminalshell_linux.c:402`) starts at `getppid()` and loops with
`ffProcessGetBasicInfoLinux(pid, &name, &ppid, &tty)`, which on Linux parses `/proc/<pid>/stat` —
the name is extracted with `memchr('(')` / `memrchr(')')` so spaces and parentheses inside it are
handled, and `ppid`/`tty` come from fields 4 and 7.

Each iteration compares the process name against the login shell's basename
(`instance.state.platform.userShell`, which is `$SHELL` when set and the passwd entry otherwise).
If the names **match**, the loop stops immediately: the login shell wins over everything else.

Otherwise the name is tested against a skip list of things that commonly sit between a terminal
and the user's shell — `init`, `sh`, `sudo`, `su`, `strace`, `gdb`, `lldb`, `lldb-mi`, `login`,
`ltrace`, `perf`, `guake-wrapped`, `time`, `clifm`, `valgrind`, `fastfetch`, `flashfetch`,
`proot`, `script`, `run-parts` (Linux only) — plus three pattern tests: any name containing
`debug`, any containing `command-not-`, and any name ending in `.sh`. A match moves to the parent
and clears the collected name.

The first name that survives both tests becomes the shell; `ffProcessGetInfoLinux()` then fills
`exe` (from `/proc/<pid>/cmdline`), `exeName` and `exePath` (from `/proc/<pid>/exe` via
`readlink`, guarded by a `ffPathExists()` check so a deleted binary is reported through the
`/proc` path instead). `ffStrbufTrimLeft(exe, '-')` strips the leading dash a login shell puts in
`argv[0]` — the step that turns `-c` into `c` in the Pitfalls example.

If the walk finds nothing, `getUserShellFromEnv()` falls back to `$SHELL` and uses its basename as
the process name. Only when both fail does the module print `Couldn't detect shell`.

### Version probes

`fftsGetShellVersion()` (`terminalshell.c:263`) dispatches on the basename of the executable.
Three strategies are used:

1. **Binary string extraction** — `ffBinaryExtractStrings()` scans the executable for a known
   literal. `bash` looks for `@(#)Bash version <x>` and takes everything up to the next `(`.
2. **Running the shell** — `ffProcessAppendStdOut()` / `ffProcessAppendStdErr()` with `--version`,
   `-version`, `-h`, or `-c 'echo $VAR'` depending on the shell, followed by per-shell parsing.
   For example `fish` skips non-numeric tokens because the message can be localised, and `ksh`
   reads the version out of the stderr banner `sh (AT&T Research) 93u+ 2012-08-01`.
3. **File version metadata** — on Windows, `getFileVersion()` reads the executable's version
   resource; `pwsh` also accepts the `$POWERSHELL_VERSION` environment variable first.

The pretty-name mapping is a small fixed table in `setShellInfoDetails()`
(`terminalshell_linux.c:284`); everything else copies `exeName`.

### Windows

`terminalshell_windows.c` performs the same walk over the NT process table, honouring
`FFTS_IGNORE_PARENT` at line 341, and resolves executables through the Win32 path APIs. The
version fallback for an unrecognised shell is `getFileVersion()` on the resolved executable.

### Caching

Both results are process-wide singletons registered with the general cache layer
(`terminalshell_linux.c:380-400` and `:439-459`). `ffDetectShell()` and `ffDetectTerminal()` begin
with `ffCacheBeginInit()`, which returns early on a hit, and `destroyShellResult()` /
`destroyTerminalResult()` release every strbuf field at each `--dynamic-interval` round boundary.
