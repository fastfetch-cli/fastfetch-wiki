# LM

> Print login manager (desktop manager) name and version

| | |
|---|---|
| Module type | `lm` |
| Default order | 20 (only used by `--gen-config`) |
| Module source | `src/modules/lm/lm.c` |
| Detection source | `src/detection/lm/` |

Prints the login manager that owns the current session: a pretty name followed by its version when one
is known. The printed key is `Login Manager` (and it is localised), while the module type is `lm`.

```
Login Manager: Logon User Interface 10.0.26100.1
```

The version is omitted from this line when it is empty. The raw service name — `LogonUI` in the sample
above, `gdm`, `sddm`, `lightdm`, … on Linux — only appears through a custom `format` or in the JSON.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `lm_linux.c` | systemd over DBus, then systemd's private session files, then the process list |
| FreeBSD / MidnightBSD / DragonFly | `lm_linux.c` | `sysctl(KERN_PROC_UID, 0)` process scan |
| NetBSD | `lm_linux.c` | `sysctl(KERN_PROC2, KERN_PROC_UID, 0)` process scan |
| OpenBSD | `lm_linux.c` | `sysctl(KERN_PROC_UID, 0)` process scan |
| Solaris / illumos | `lm_linux.c` | Walks `/proc/<pid>/psinfo` |
| GNU/Hurd | `lm_linux.c` | Walks `/proc/<pid>/comm` |
| macOS | `lm_apple.m` | Fixed value, plus a version from an `Info.plist` |
| Windows | `lm_windows.c` | Fixed value, plus the `LogonUI.exe` file version |
| Android | `lm_nosupport.c` | Reports "Not supported on this platform" |
| Haiku | `lm_nosupport.c` | Not supported |

The same `lm_linux.c` serves every POSIX platform: only the process-enumeration call differs per
platform, and the systemd route is guarded by `__linux__ && !__ANDROID__`.

## Configuration

The module has no options of its own.

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | `Login Manager` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

## Format string

Run `fastfetch -h lm-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{service}` | Service or process name as it was found, e.g. `gdm3`, `LogonUI` |
| `{pretty-name}` | Mapped display name, e.g. `GDM`, `Logon User Interface` |
| `{version}` | Version string; empty when none was found |

None of the three is available in the key format.

## JSON output

```jsonc
[
    {
        "type": "LM",
        "result": {
            "service": "LogonUI",
            "prettyName": "Logon User Interface",
            "version": "10.0.26100.1"
        }
    }
]
```

`result` is an object, not an array — there is at most one login manager. `version` is an empty string
when it could not be determined. When no login manager was found the module writes
`{ "type": "LM", "error": "No LM service found" }`, the same message the text path shows.

## Examples

```jsonc
// Keep the service name visible next to the pretty name
{ "type": "lm", "format": "{pretty-name} ({service})" }
```

```jsonc
// A version-only line, with the key hidden
{ "type": "lm", "key": " ", "format": "{version}" }
```

```jsonc
// Treat an empty version as a missing variable
{ "type": "lm", "format": "{pretty-name}{?version} {version}{?}" }
```

## Pitfalls

- **The process-list route only knows 16 names.** A process is recognised only if its name is exactly
  one of `atrium`, `cdm`, `entrance`, `gdm`, `gdm3`, `greetd`, `lemurs`, `lightdm`, `lxdm`, `ly`,
  `plasmalogin`, `sddm`, `slim`, `tbsm`, `xdm` or `xenodm` (case-insensitively). Anything else — a
  custom or a newly renamed display manager — is reported as `No LM service found` even while it is
  running.
- **The process scan only looks at root-owned processes.** On Linux a pid is skipped unless
  `/proc/<pid>/loginuid` reads "no login uid" (`4294967295`), which in practice leaves root-owned system
  daemons; the BSD variants ask for uid `0` explicitly, and Solaris skips every `psinfo` whose `pr_uid`
  is not `0`. A per-user login manager is therefore invisible.
- **`{pretty-name}` falls back to the raw service name.** Only the 16 names above (and `gdm`-prefixed,
  `sddm`-prefixed, `xfwm`-prefixed and `lightdm` services coming from systemd) get a mapped display
  name; everything else is printed verbatim, so a session running `sshd` prints `sshd` twice.
- **Version lookups both run programs and cover only five prefixes.** `gdm` / `gdm3` (runs
  `gdm3 --version`, falling back to `gdm --version`), `lightdm` (runs `lightdm --version` and reads
  stderr), `xfwm` (runs `xfwm4 --version`), `sshd` (runs `sshd -V`, reads stderr, keeps what is between
  the first `_` and the first `,`), and `sddm` (decompresses `/usr/share/man/man1/sddm.1.gz` and parses
  its `.TH` line). Everything else gets an empty version, and each of these parses a fixed output shape
  with string surgery, so a changed output format degrades to an empty or wrong version rather than an
  error.
- **`xfwm` and `sshd` are unreachable through the process-list route.** Both have version lookups but
  neither is in the 16-name table, so those versions only ever appear when the service name comes from
  systemd (a `sshd` session, for instance).
- **SDDM's version depends on a hardcoded man page path and on libz.** It is read from
  `/usr/share/man/man1/sddm.1.gz`; a build without libz reports `Fastfetch is built without libz support`
  internally and simply ends up with an empty version, and an install whose man pages live elsewhere
  (a non-`/usr` prefix) is not found.
- **`--no-detect-version` is honoured everywhere except macOS.** `lm_apple.m` reads
  `CFBundleShortVersionString` from `/System/Library/CoreServices/loginwindow.app/Contents/Info.plist`
  unconditionally, while the Linux and Windows backends check `detectVersion` before spawning anything
  or reading a file version.
- **On Linux without DBus, the private systemd files are the next resort.** `$XDG_SESSION_ID` is used,
  and when it is unset the session id is guessed from `/run/systemd/users/<uid>`'s `DISPLAY=` property.
  The source comments call this "private data. Do not parse" and describe the guess as "actually buggy,
  and assumes current user is using DE" — a multi-seat machine can therefore report the wrong session's
  service.
- **Windows and macOS always report something.** Both short-circuit the whole detection with a constant
  (`LogonUI` / `Logon User Interface`, `loginwindow` / `Login Window`) and only the version is read from
  the system, so `No LM service found` cannot happen there.

## Implementation

`ffDetectLM()` fills an `FFLMResult` — three `FFstrbuf`s, `service`, `prettyName` and `version` — and
returns an error string. `ffPrintLM()` and `ffGenerateLMJsonResult()` each call it themselves; nothing is
cached, so a `--dynamic-interval` run repeats the whole lookup every round.

The detection is a chain of three (Linux) or one (all other POSIX platforms) sources, followed by two
lookup tables:

1. Get a service name from the first source that works.
2. Map it to a pretty name, falling back to the service name itself.
3. If `detectVersion` is on, look the version up by service-name prefix.

### Linux

In order:

- **systemd over DBus** (only when built with DBus support): reads the `Service` property of
  `/org/freedesktop/login1/session/auto` from `org.freedesktop.login1` on the system bus.
- **systemd's private files**: `SERVICE=` from `/run/systemd/sessions/<session id>`, where the session id
  comes from `$XDG_SESSION_ID` or from the `DISPLAY=` line of `/run/systemd/users/<uid>`.
- **The process list**: `/proc` is walked, keeping only directories whose name starts with a digit and
  whose `loginuid` reads "no login uid"; `<pid>/comm` is then matched against the name table.

The pretty-name mapping is a `switch` on the first character of the service name, and the version lookup
a chain of prefix comparisons (both described in the Pitfalls above).

### FreeBSD / MidnightBSD / DragonFly, NetBSD, OpenBSD

The process list comes from `sysctl()`: `KERN_PROC_UID` with uid `0` (NetBSD uses `KERN_PROC2`), yielding
a `kinfo_proc` array whose `ki_comm` (NetBSD: `p_comm`) is matched against the name table. On DragonFly
`ki_comm` is a macro for `kp_comm`. The first match wins.

### Solaris / illumos and GNU/Hurd

Both walk `/proc`. Solaris reads `sizeof(psinfo_t)` bytes from each `<pid>/psinfo` and matches
`pr_fname` after skipping every entry whose `pr_uid` is not `0`. GNU/Hurd reads `<pid>/comm`, which makes
its scan effectively the same as the Linux fallback.

### macOS

The constants are hardcoded: `loginwindow` / `Login Window`. The version is
`CFBundleShortVersionString` from
`/System/Library/CoreServices/loginwindow.app/Contents/Info.plist`, read through `NSDictionary`; when the
file cannot be read the version stays empty.

### Windows

The constants are hardcoded: `LogonUI` / `Logon User Interface`. When `detectVersion` is on, the file
version of `%SystemRoot%\system32\LogonUI.exe` is read with `ffGetFileVersion()`, which is the same
mechanism the `Kernel` module uses for its version.
