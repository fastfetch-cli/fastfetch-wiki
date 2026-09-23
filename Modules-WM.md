# WM

> Print the window manager name and version

| | |
|---|---|
| Module type | `wm` |
| Default order | 22 (only used by `--gen-config`) |
| Module source | `src/modules/wm/wm.c` |
| Detection source | `src/detection/displayserver/` (the name), `src/detection/wm/` (version and plugin) |

Prints the window manager or Wayland compositor that owns the session, its version, the
display protocol in use, and any recognised tiling add-on.

```
Window Manager: KWin (X11)
Window Manager: Hyprland 0.48.1 (Wayland)
Window Manager: Quartz Compositor 1.600.0 (with Rectangle 0.80)
Window Manager: dwm.exe 10.0.22621.2506
```

## Platform support

| Platform | WM name | Version probe | Plugin probe |
|---|---|---|---|
| Linux | `displayserver_linux.c` | `wm_linux.c` | not supported |
| Android | `displayserver_android.c` | `wm_linux.c` | not supported |
| FreeBSD / NetBSD / OpenBSD | `displayserver_linux.c` | `wm_linux.c` | not supported |
| GNU/Hurd | `displayserver_linux.c` | `wm_linux.c` | not supported |
| Solaris / illumos | `displayserver_linux.c` | `wm_nosupport.c` | not supported |
| Haiku | `displayserver_haiku.cpp` | `wm_nosupport.c` | not supported |
| macOS | `displayserver_apple.c` | `wm_apple.m` | `wm_apple.m` |
| Windows | `displayserver_windows.c` | `wm_windows.c` | `wm_windows.c` |

`wm_nosupport.c` is registered in the Solaris and Haiku blocks, so the module reports no
version there. This is the mirror image of the `DE` module, which is unsupported on macOS,
Windows and Haiku instead — no platform has both.

Solaris and Haiku still print a window manager name; only `{version}` stays empty.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `detectPlugin` | boolean | `true` | Scan for a recognised tiling window manager add-on. |
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

Version detection is controlled by the global `general.detectVersion` setting.

`detectPlugin` is accepted on every platform, but only macOS and Windows have a plugin probe —
on the other eight it is silently ignored, because the Linux implementation of
`ffDetectWMPlugin()` returns `Not supported on this platform` without touching the buffer.

## Format string

Run `fastfetch -h wm-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{process-name}` | The session process, e.g. `kwin_wayland` or `dwm.exe` |
| `{pretty-name}` | The display name, e.g. `KWin` |
| `{protocol-name}` | `X11`, `Wayland`, `TTY` or `SurfaceFlinger` |
| `{plugin-name}` | Recognised tiling add-on, when one was found |
| `{version}` | Version string, when one could be determined |

`{protocol-name}` is empty on macOS, Windows and Haiku — only the Linux/BSD/Solaris display
server layer records a protocol.

## JSON output

```jsonc
{
    "type": "WM",
    "result": {
        "processName": "WindowServer",
        "prettyName": "Quartz Compositor",
        "protocolName": "",
        "pluginName": "",
        "version": "1.600.0"
    }
}
```

On failure the object is `{ "type": "WM", "error": "No WM found" }`.

## Examples

```jsonc
{ "type": "wm", "key": "WM", "format": "{pretty-name} {version} ({protocol-name})" }
```

```jsonc
{ "type": "wm", "detectPlugin": false, "format": "{process-name}" }
```

## Pitfalls

- **Most window managers get no version.** The Linux version probe knows exactly eleven names
  — `Hyprland`, `sway`, `labwc`, `niri`, `umbriel`, `weston`, `WSLg`, `i3`, `ctwm`, `fvwm` and
  `Openbox` — and returns `Unsupported WM` for everything else. KWin, Mutter, Muffin, Marco,
  Xfwm4, dwm, bspwm, Qtile, IceWM and the rest print a name with no version. macOS only knows
  `WindowServer` and Windows only knows `dwm.exe`.
- **The probe matches the process name, not the pretty name.** The dispatch compares against
  `wmProcessName`, which for a Wayland session is whatever `/proc/<pid>/comm` reported for the
  socket owner. A compositor started through a wrapper or renamed binary therefore falls through
  to `Unsupported WM`.
- **`{plugin-name}` is a tiling add-on, not a window manager plugin.** It is only ever set for
  Rectangle, yabai, AeroSpace, Amethyst and GlazeWM on macOS, and for FancyWM, GlazeWM and
  Komorebi on Windows. On Linux and BSD the field is always empty.
- **The plugin probe's errors are swallowed.** `ffDetectWMPlugin()` returns a status string, but
  both the text and the JSON paths ignore it — a failed process enumeration simply produces an
  empty `{plugin-name}` with no diagnostic.
- **On macOS the plugin probe requires a parent PID of 1.** Only processes whose `e_ppid` is 1
  are considered, so a tiling add-on started by a session manager or a terminal is missed.
- **The plugin name is capitalised by hand.** On macOS, when no `Info.plist` version can be
  read, the first character of the process name is upper-cased in place — so `yabai` is reported
  as `Yabai` and `rectangle` as `Rectangle`.
- **`{pretty-name}` can be an arbitrary string.** An unrecognised window manager falls back to
  its raw process name, and an unrecognised `XDG_CURRENT_DESKTOP` can be promoted into the WM
  slot by the display-server layer, so `{pretty-name}` is not restricted to the known names.
- **`detectPlugin: false` does not stop the process scan.** It only skips the plugin probe; the
  window manager name itself still comes from a full process-table walk when the display server
  connection could not supply it.
- **Version detection runs external commands.** `sway`, `labwc`, `niri`, `umbriel`, `weston`,
  `i3`, `ctwm`, `fvwm` and `Openbox` are all executed with `--version` when the binary string
  scan fails. Set `general.detectVersion` to `false` to keep fastfetch side-effect free.

## Implementation

`ffPrintWM()` and `ffGenerateWMJsonResult()` both ask `ffConnectDisplayServer()` for the cached
`FFDisplayServerResult`, error out when `wmPrettyName` is empty, then optionally call
`ffDetectWMPlugin()` and `ffDetectWMVersion()`. The version call is gated on
`instance.config.general.detectVersion` inside the module itself.

### Where the name comes from

The display-server layer fills `wmProcessName`, `wmPrettyName` and `wmProtocolName`. On Linux,
Android, BSD and Solaris, `ffdsDetectWMDE()` in `wmde.c` first uses whatever the display-server
connection already produced, otherwise the environment value from `parseEnv()`, and then falls
back to a process scan. `applyPrettyNameIfWM()` maps about twenty process names to pretty names
with case-insensitive matching, including the `kwin_*`, `cinnamon-*` and `-kwin_wayland` prefix
and suffix forms. Anything unrecognised keeps its raw process name as the pretty name.

`wmProtocolName` is set by the display-server backends, not by the WM module: `Wayland`, `X11`
or `TTY` on Linux, and `SurfaceFlinger` on Android. On Android the whole detection is skipped
when the protocol is `SurfaceFlinger`, since only the X11 path can report a window manager.

### Where the version comes from

`ffDetectWMVersion()` is an `if`/`else if` chain over the process name:

- **Hyprland** — reads `#define GIT_TAG` from `<pkg base>/include/hyprland/src/version.h`,
  then scans the `Hyprland` binary for a `v<major>.<minor>.<patch>` string, then runs
  `Hyprland --version` and prefers a `Tag: v...` line over the first field of the output.
- **sway** — scans the `sway` binary for a line starting with `sway version `, and also
  recognises the SwayFX form `swayfx version <v> (based on sway <v>)`, which is rendered as
  `<sway> [swayfx <swayfx>]`. Falls back to `sway --version`.
- **labwc / ctwm / fvwm / Openbox** — share `extractCommonWmVersion()`, which scans the binary
  for a `<major>.<minor>.<patch>` string. The command fallbacks differ: `labwc --version` keeps
  the second field, the other three keep the last field of the first line, and fvwm is invoked
  with `-version` rather than `--version`.
- **niri / umbriel** — run `<name> --version` and keep the text between the first space and the
  last `(`.
- **weston** — runs `weston --version` and keeps the last field of the first line.
- **WSLg** — parses `/mnt/wslg/versions.txt`, handling both the `WSLg: <v>` form used from
  WSL 2.9.3 on and the older `WSLg <v>+<build>: <v>` form.
- **i3** — scans the `i3` binary for a `<major>.<minor>` string, then runs `i3 --version` and
  takes the token after `version `.
- **macOS** — only for `WindowServer`: reads `CFBundleShortVersionString` from the SkyLight
  `version.plist`, falling back to the CoreGraphics one.
- **Windows** — only for `dwm.exe`: reads the file version of `%SystemRoot%\dwm.exe`.

Binary string scanning is preferred over spawning processes because it avoids the fork; the
scans use `ffBinaryExtractStrings()` with a `sscanf`-based matcher that only accepts a string
starting with a full version number, and the minimum-length argument is set to the length of a
representative sample (`v0.0.0`, `sway version 0.0.0`, `0.0`, …) so that unrelated strings are
rejected early.

### Where the plugin name comes from

The macOS probe walks `sysctl({CTL_KERN, KERN_PROC, KERN_PROC_ALL})` and considers only
processes whose parent is PID 1. The Windows probe walks the process table with
`SystemBasicProcessInformation` (smaller entries, so one query suffices) and falls back to
`SystemProcessInformation` with up to five buffer retries. Both then match against a fixed list
and, when `general.detectVersion` is on, append the add-on's own version — from
`Info.plist` via `ffGetAppNameAndVersion()` on macOS, from `ffGetFileVersion()` or
`komorebi --version` on Windows.
