# Terminal

> Print the current terminal name and version

| | |
|---|---|
| Module type | `terminal` |
| Default order | 29 (only used by `--gen-config`) |
| Module source | `src/modules/terminal/terminal.c` |
| Detection source | `src/detection/terminalshell/` (shared with the `Shell` module) |

Prints the pretty name of the terminal fastfetch is running inside, with its version when one could
be determined.

```
Terminal: Alacritty 0.15.1
Terminal: WezTerm 20240203-110809-5046fc22
Terminal: kitty 0.39.1
Terminal: dumb
```

The name is the *pretty name*, not the process name: `wezterm-gui` prints as `WezTerm`,
`tmux: server` as `tmux`, `gnome-terminal-server` as `GNOME Terminal`, `com.termux` as `Termux`,
`WindowsTerminal.exe` as `Windows Terminal` and `conhost` as `Windows Console`. The version is
appended only when the backend could determine one, so a bare `Terminal: dumb` is a normal result,
not a failure.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `terminalshell_linux.c` | Walks `/proc/<pid>/stat` up the parent chain, then falls back to environment variables |
| Android | `terminalshell_linux.c` | Same file; adds `Termux` handling |
| FreeBSD / NetBSD / OpenBSD / DragonFly | `terminalshell_linux.c` | Same file; the shared helper uses `sysctl` on these |
| Solaris / illumos | `terminalshell_linux.c` | Same file |
| macOS | `terminalshell_linux.c` | Same file; the helper uses `KERN_PROC_PID` |
| Haiku | `terminalshell_linux.c` | Same file |
| GNU/Hurd | `terminalshell_linux.c` | Same file |
| Windows | `terminalshell_windows.c` | Walks the NT process table, then the `Console\%%Startup` registry key |

Every platform has an implementation, but the Unix one is only *partly* a process walk: when the
parent chain does not produce a terminal it reads environment variables, and on Windows the
process walk is skipped entirely inside an MSYS shell — see the pitfalls.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | `Terminal` | Module key. A single space hides the key and the separator |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

There are no module-specific options. The detection itself is steered by environment variables and
by one global setting:

| Variable / setting | Effect |
|---|---|
| `general.detectVersion` | Global switch for all version probes. `false` skips them entirely, which also stops the module from running the terminal binary |
| `FFTS_IGNORE_PARENT=1` | Skips one extra level of the parent chain before looking for the terminal (shared with `Shell`) |
| `SSH_TTY` | Used when connected over SSH; the value is also used as the pretty name for `sshd` |
| `KITTY_PID`, `KITTY_INSTALLATION_DIR` | Selects `kitty` and resolves its real PID |
| `WT_SESSION`, `WT_PROFILE_ID` | Selects Windows Terminal (Linux/WSL path) |
| `ConEmuPID` | Selects ConEmu and resolves its real PID (both platforms) |
| `ALACRITTY_SOCKET`, `ALACRITTY_LOG`, `ALACRITTY_WINDOW_ID` | Selects Alacritty |
| `TERMUX_VERSION`, `TERMUX_MAIN_PACKAGE_FORMAT`, `TERMUX_APP__PID` | Selects Termux (Android) |
| `KONSOLE_VERSION` | Selects konsole (Linux/BSD/GNU) |
| `GNOME_TERMINAL_SCREEN`, `GNOME_TERMINAL_SERVICE` | Selects gnome-terminal (Linux/BSD/GNU) |
| `TERM_PROGRAM`, `TERM_PROGRAM_VERSION` | Generic selector used by macOS and mintty, and the generic version source |
| `LC_TERMINAL`, `LC_TERMINAL_VERSION` | Second generic selector / version source |
| `TERM` | Last resort. When it is unset or `linux`, `ttyname()` on stdout is used instead |

## Format string

Run `fastfetch -h terminal-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{process-name}` | Terminal process name, as the kernel reports it |
| `{exe}` | First argument of the terminal's command line (`arg0` on Unix) |
| `{exe-name}` | Basename of `{exe}` |
| `{pid}` | Terminal process ID |
| `{pretty-name}` | The mapped display name (what the default output prints) |
| `{version}` | Terminal version, empty when it could not be determined |
| `{exe-path}` | Full path of the terminal executable |
| `{tty}` | The tty / pts used, as a **path** |

None of the eight is marked `*` in the help output, so none is available in the key format.

`{exe-name}` and `{exe-path}` are frequently empty — see the pitfalls. A format that prints them
unconditionally produces empty brackets:

```jsonc
{ "type": "terminal", "format": "[{process-name}][{exe}][{exe-name}][{pid}][{pretty-name}][{version}][{exe-path}][{tty}]" }
```

```
Terminal: [dumb][dumb][][0][dumb][][][]
```

## JSON output

```jsonc
[
    {
        "type": "Terminal",
        "result": {
            "processName": "dumb",
            "exe": "dumb",
            "exeName": "",
            "exePath": "",
            "pid": 0,
            "ppid": 0,
            "prettyName": "dumb",
            "version": "",
            "tty": ""
        }
    }
]
```

- All nine keys are always present. Unknown values are the empty string or `0`; the module never
  emits `null`.
- On failure the object carries `error` instead and has no `result`.
- **`tty` is a string here** (`/dev/pts/0`), unlike the `Shell` module's numeric `tty`. The two are
  not interchangeable.
- `ppid` has no format variable, so it is only reachable through JSON.
- The key order is `processName`, `exe`, `exeName`, `exePath`, `pid`, `ppid`, `prettyName`,
  `version`, `tty`.

## Examples

```jsonc
// Name and version only, no fallback to the process name
{ "type": "terminal", "format": "{pretty-name} {version}" }
```

```jsonc
// Which tty and which binary
{ "type": "terminal", "format": "{pretty-name} on {tty} ({exe-path})" }
```

```jsonc
// Machine-readable: skip the version probe entirely
{ "type": "terminal", "format": "{process-name}\t{pid}" }
```

## Pitfalls

- **The module answers with `$TERM` when it cannot find a real terminal.** If the parent chain
  yields nothing, `TERM` is used verbatim — so a non-interactive shell, a CI runner or a scripted
  environment prints `Terminal: dumb` (or `xterm-256color`) instead of an error. That is the
  documented last-resort path, not a detection failure.
- **`{exe-name}` and `{exe-path}` are frequently empty.** Only the process-walk path fills
  `exePath`; the environment path leaves it empty. On Windows that path also sets `exeName` to
  `""` explicitly, while on Unix `exeName` still points at the start of `exe` — so it ends up
  being the whole value when that value contains no `/`, and the part after the last `/`
  otherwise. A format that prints both unconditionally has to tolerate empty output.
- **Terminal detection depends on shell detection.** `ffDetectTerminal()` starts from
  `ffDetectShell()->ppid`. A wrapper mistaken for the shell (see `Modules/Shell`) therefore also
  produces a wrong — or missing — terminal, and `FFTS_IGNORE_PARENT=1` is the shared workaround.
- **Inside an MSYS shell on Windows the process walk is skipped.** `getTerminalInfo()` returns
  immediately when `MSYSTEM` is set, because MSYS does not follow the process tree of native
  Windows programs. Detection falls back to `ConEmuPID`, `SSH_CONNECTION`, `WT_SESSION`,
  `ALACRITTY_*`, `TERM_PROGRAM`, `TERM`, and finally to the registry / conhost probe.
- **`{tty}` is the tty of *stdout*, not of the terminal process.** On Unix it is captured with
  `ttyname(STDOUT_FILENO)` during initialisation, before any process walk, so it describes where
  fastfetch is writing rather than what the terminal is attached to. Redirect stdout to a file and
  it stays empty even though a terminal was found. On Windows it is **never set at all** — the
  field is initialised and then left alone, so `{tty}` is always empty there.
- **Version detection runs the terminal binary for many terminals.** `gnome-terminal`,
  `xfce4-terminal`, `kgx`, `konsole`, `foot`, `mate-terminal`, `cockpit-bridge`, `xterm`,
  `blackbox`, `st`, `urxvt`, `weston-terminal`, `lxterminal`, `tmux`, `zellij`, `zed`, `kitty`,
  `wezterm`, `contour`, `sakura`, `termite`, `ptyxis` and `tilix` are all executed (with `--version`,
  `-v`, `-V` or a `+kitten query-terminal` sub-command) when `general.detectVersion` is on. Several
  of them are only executed after a binary-string scan fails, so the cost is usually one process.
- **The version table is a fixed list.** Only the terminals named above plus `alacritty`, `screen`,
  `sshd`, `Tabby`, `Terminal` (Haiku), `WindowsTerminal.exe`, `ConEmu*`, `warp.exe`, and the
  `TERM_PROGRAM_VERSION` / `LC_TERMINAL_VERSION` pairs have a probe. Anything else prints the name
  with no version and no explanation.
- **`TERM_PROGRAM_VERSION` is trusted only when the program name matches.** The variable is used
  when `processName` starts with `TERM_PROGRAM`, with two special cases: `vscode` matches `code*`,
  and on macOS `iTerm.app` matches `iTermServer-*`. A mismatch silently falls through to the next
  probe.
- **Windows Terminal's profile is what decides the default terminal, not the process tree.** When
  no terminal is found, `detectDefaultTerminal()` reads
  `HKCU\Console\%%Startup` → `DelegationTerminal`; the two well-known "let Windows decide" /
  "Conhost" GUIDs jump straight to the conhost probe, and a `Microsoft.WindowsTerminal*` package
  name is resolved to `<ProgramFiles>\WindowsApps\<package>\WindowsTerminal.exe`. The conhost probe
  itself uses `NtQueryInformationProcess(ProcessConsoleHostProcess)`, which for Windows Terminal
  reports the PID of `OpenConsole`.
- **A `conhost` that was spawned by Explorer is deliberately reported as "no terminal".** The walk
  treats `sihost`, `explorer`, `wininit` and `services` as placeholders and returns nothing, so the
  module then goes through the env / registry path instead of naming `conhost`.
- **`sshd` prints the tty as its pretty name.** Over SSH, `sshd`'s pretty name is replaced by
  `$SSH_TTY` when it is set, and stays `sshd` otherwise. That is why an SSH session can show
  `Terminal: /dev/pts/3`.
- **Terminal detection is re-run on every `--dynamic-interval` round.** The result lives in an
  `FFcache` entry that is dropped at each round boundary, so an environment change is picked up
  without restarting fastfetch. Single-shot runs are unaffected.
- **Errors are invisible by default.** `Couldn't detect terminal` goes through `ffPrintError()` and
  therefore needs `display.showErrors` to be `true` to be seen; otherwise the module simply
  disappears from the output.

## Implementation

`ffDetectTerminal()` (`terminalshell_linux.c` / `terminalshell_windows.c`) fills a process-wide
`FFTerminalResult` — `processName`, `exe`, `exeName` (a pointer *into* `exe`), `exePath`,
`prettyName`, `version`, `tty`, `pid`, `ppid` — and `ffPrintTerminal()` prints
`prettyName + " " + version`, or just `prettyName` when there is no version.
`ffGenerateTerminalJsonResult()` writes all nine fields, so the JSON is a superset of the format
variables (`ppid` is the extra one).

The result is registered with the general cache layer (`FFcacheEntryTerminal`) and re-detected at
every `--dynamic-interval` boundary.

### Unix

1. `ffDetectShell()` runs first; its `ppid` is the starting point.
2. `getTerminalInfo()` walks up with `ffProcessGetBasicInfoLinux(pid, &name, &ppid, nullptr)`,
   skipping every known shell name (`sh`, `ash`, `bash`, `zsh`, `ksh`, `mksh`, `oksh`, `csh`,
   `tcsh`, `fish`, `dash`, `pwsh`, `nu`, `git-shell`, `elvish`, `oil.ovm`, `xonsh`, `login`,
   `clifm`, `chezmoi`, `proot`, `script`), `sudo`/`su`, `init`/`systemd`, and on Linux also
   `Relay(`, `flatpak-`, `run-parts` and any name ending in `.sh`. The first survivor is the
   terminal; `ffProcessGetInfoLinux()` then fills `exe`, `exeName` and `exePath`.
3. If nothing survived, `getTerminalFromEnv()` walks the environment list from the table above.
   Only a `processName` that is still `login`, `systemd`, `init`, `launchd` (macOS),
   `SessionLeader` or `0` is discarded and replaced by the env result.
4. `setTerminalInfoDetails()` maps the pretty name — including the Nixpkgs special case, where a
   `/nix/store` `exePath` makes the name after the last `-` the process name.

`tty` is initialised from `ttyname(STDOUT_FILENO)` in step 0, before any of the above.

### Windows

`getTerminalInfo()` walks the NT process table and stops at the first **GUI** process; a non-GUI
parent is skipped as a nested shell. The same `sihost` / `explorer` / `wininit` / `services` check
decides whether the walk gives up. When it does, `getTerminalFromEnv()` and then
`detectDefaultTerminal()` run. `prettyName` starts as `processName` with a trailing `.exe` trimmed,
and `setTerminalInfoDetails()` maps it (`WindowsTerminal` → `Windows Terminal` or
`Windows Terminal Preview` depending on the package directory, `conhost` → `Windows Console`,
`Code` → `Visual Studio Code`, `wezterm-gui` → `WezTerm`).

### Version probes

`fftsGetTerminalVersion()` dispatches on the process name and uses three strategies: running the
binary, scanning it for a version literal (`ffBinaryExtractStrings()`), and the
`TERM_PROGRAM_VERSION` / `LC_TERMINAL_VERSION` environment variables. `kitty` additionally asks the
terminal itself over the escape-code protocol and `foot` reads the version out of the terminal's
DA response before falling back to the binary.
