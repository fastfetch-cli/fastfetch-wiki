# InitSystem

> Print init system (pid 1) name and version

| | |
|---|---|
| Module type | `initsystem` |
| Default order | 10 (only used by `--gen-config`) |
| Module source | `src/modules/initsystem/initsystem.c` |
| Detection source | `src/detection/initsystem/` |

Prints the name of the process the kernel starts first, with its version when one could be
determined.

```
Init System: systemd 257.1-1-arch
Init System: launchd 7.0.0
Init System: OpenRC
```

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `initsystem_linux.c` | `/proc/1/*` via the shared process helpers |
| Android | `initsystem_linux.c` | Falls back to `/system/bin/init` when `/proc/1` is unreadable |
| FreeBSD / NetBSD / OpenBSD / DragonFly | `initsystem_linux.c` | Same `/proc/1` reader |
| Solaris / illumos | `initsystem_linux.c` | Same `/proc/1` reader |
| GNU/Hurd | `initsystem_linux.c` | Same `/proc/1` reader |
| macOS | `initsystem_linux.c` | `/proc/1` reader, with a `launchctl` version probe |
| Windows | `initsystem_windows.c` | Finds `smss.exe` by walking the process list |
| Haiku | `initsystem_haiku.c` | Hard-codes `/boot/system/servers/launch_daemon` |

`src/detection/initsystem/initsystem_nosupport.c` exists in the tree but is **not listed in
`CMakeLists.txt`** anywhere, so it is never compiled — the module has a real implementation on
all ten platforms.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

There are no module-specific keys. Version detection is controlled by the global
`general.detectVersion` setting.

## Format string

Run `fastfetch -h initsystem-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{name}` | Init system name |
| `{exe}` | Absolute path of the init binary |
| `{version}` | Version string, when it could be determined |
| `{pid}` | Process ID of the init process |

## JSON output

```jsonc
{
    "type": "InitSystem",
    "result": {
        "name": "systemd",
        "exe": "/usr/lib/systemd/systemd",
        "version": "257.1-1-arch",
        "pid": 1
    }
}
```

## Examples

```jsonc
{ "type": "initsystem", "format": "{name} {version}" }
```

```jsonc
{ "type": "initsystem", "key": "Init", "format": "{exe} (pid {pid})" }
```

## Pitfalls

- **`{version}` is empty for most init systems.** The Linux reader only knows how to extract a
  version for systemd, dinit and shepherd; everything else (OpenRC, runit, s6, SysV init,
  BusyBox init, …) prints the name alone with no explanation.
- **Version detection starts child processes.** For systemd (when the string scan fails), dinit
  and shepherd it runs the init binary with `--version`, and on macOS it runs
  `/bin/launchctl version`. Set `general.detectVersion` to `false` if you need fastfetch to
  stay side-effect free.
- **`{pid}` is only 1 on Unix-like systems.** Windows reports the real PID of `smss.exe`, and
  Haiku reports the Haiku *team* ID of `launch_daemon`. A format that assumes 1 is wrong on
  those two.
- **`{exe}` is a resolved symlink on Linux.** `/sbin/init` is followed with `realpath()`, and
  the name is then re-derived from the resolved path — which is how a distribution that ships
  systemd as `/sbin/init` still reports `systemd`.
- **The Windows name is lower-case.** The reader strips the `.exe` suffix, so the output is
  `smss`, not `smss.exe` or `Session Manager`. That is the process the Windows kernel starts
  first, not the shell or `wininit.exe`.
- **`-h initsystem-format` mislabels `{version}`.** The description reads
  "Init system version path", which is a copy-paste slip — the variable is a version string,
  and no path is involved.

## Implementation

### Linux, Android, BSD, Solaris, GNU/Hurd and macOS

`ffDetectInitSystem()` reads pid 1 in two passes. `ffProcessGetBasicInfoLinux(1, &name, …)`
parses `/proc/1/stat` for the short name, then `ffProcessGetInfoLinux(1, &name, &exe, …)` —
with the comment that `/proc/1/exe` is **not** readable for this particular process — reads
`/proc/1/cmdline` instead to get the executable path.

When the resulting `exe` is absolute, `realpath()` is applied and both `exe` and `name` are
re-derived from the resolved path, since `/sbin/init` is usually a symlink to the real
implementation.

The Android carve-out runs only if the two reads above failed: when `/system/bin/init` exists,
that path is used and the name is fixed to `init`, because Android's pid 1 does not expose the
usual `/proc` entries.

Version extraction is gated on `instance.config.general.detectVersion` and is
platform-conditional:

- **systemd** — `ffBinaryExtractStrings()` scans the init binary for the literal
  `systemd <version> running in <platform>` and slices the version out of it. That avoids
  running anything in the common case. When the scan finds nothing, `systemctl --version` is
  executed — or the init binary itself when it ends with `/systemd`, so that a user with a
  second systemd installed elsewhere still gets the right answer. Only the text between the
  first `(` and the next `)` is kept.
- **dinit** — runs `dinit --version` and parses `Dinit version 0.18.0.` by cutting at the first
  newline, trimming a trailing dot and taking the last space-separated token.
- **shepherd** — runs `shepherd --version`, optionally skipping a first line that does not
  start with `shepherd`, then takes the last token of the first remaining line.
- **macOS launchd** — runs `/bin/launchctl version` and extracts what follows `Version ` up to
  the next `:`.

### Windows

There is no pid-1 equivalent, so the reader scans the process list for the first user-space
process: `smss.exe`, identified by `InheritedFromUniqueProcessId == 4` (`System`) and a name
ending in `.exe`.

`SystemBasicProcessInformation` is preferred over `SystemProcessInformation` because its
entries are much smaller and 1024 of them fit in a modest stack buffer. Both loops walk the
linked list with `NextEntryOffset` and stop when the offset is zero — the list's last entry
always has offset 0, even when the buffer was truncated, which is what keeps a truncated result
from being read past the end.

Because the process cannot be opened with `PROCESS_QUERY_INFORMATION` rights for pid 1-class
processes, the executable path is reconstructed as
`%SystemRoot%\system32\<name>` from `SharedUserData->NtSystemRoot`, and the version comes from
`ffGetFileVersion()` on that path. The `.exe` suffix is stripped from `{name}` but kept in
`{exe}`.

A `SYSTEM_PROCESS_INFORMATION` scan is kept as a fallback for Windows versions where the
smaller information class is unavailable; it is selected by `ffIsSystemBasicProcessInfoAvailable()`.

### Haiku

Haiku cannot be queried through the roster because `launch_daemon` starts before the registrar
exists, so the path `/boot/system/servers/launch_daemon` is hard-coded and checked with
`ffPathExists()`. The PID is then found by walking every team with `get_next_team_info()` and
comparing `teamInfo.args` against that path — a team whose arguments match is the init process.
If no team matches, `{pid}` stays `0` rather than being reported as 1. The version comes from
`ffGetFileVersion()`, gated on `general.detectVersion` like the other platforms.
