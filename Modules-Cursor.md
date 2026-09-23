# Cursor

> Print cursor style name

| | |
|---|---|
| Module type | `cursor` |
| Default order | 27 (only used by `--gen-config`) |
| Module source | `src/modules/cursor/cursor.c` |
| Detection source | `src/detection/cursor/` |

Prints the mouse-cursor theme and, when the backend knows it, the cursor size:

```
Cursor: Windows Default (32px)
```

The theme is post-processed before it is printed, because the backends hand over whatever string the
desktop environment stored: a trailing `cursors` and then a trailing `cursor` are removed
case-insensitively, then trailing `_` and `-` are trimmed, and if nothing is left the theme becomes
`default`. `Adwaita-cursors` therefore prints as `Adwaita`, and a backend that reports an empty
theme prints `default` rather than nothing.

The size part is optional in two ways: it is dropped when the backend produced no size at all, and
also when the size is the exact string `0`. The suffix is always formatted as ` (Npx)` — the number
is written verbatim, the `px` is added by the module, so a size that already carries a unit would be
printed with two of them.

The module is a single-line module: the index passed to `ffPrintLogoAndKey()` is always `0`, so the
key never gets a number.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `cursor_linux.c` | GTK/Qt settings, X resources, environment variables |
| Android | `cursor_linux.c` | Same code; every path below needs a display server |
| FreeBSD | `cursor_linux.c` | Same code |
| NetBSD | `cursor_linux.c` | Same code |
| OpenBSD | `cursor_linux.c` | Same code |
| Solaris / illumos | `cursor_linux.c` | Same code |
| GNU/Hurd | `cursor_linux.c` | Same code |
| Haiku | `cursor_nosupport.c` | Not supported |
| macOS | `cursor_apple.c` | `CFPreferences`, `com.apple.universalaccess` |
| Windows | `cursor_windows.c` | Registry, `HKCU\Control Panel\Cursors` |

The Haiku stub keeps the module registered and reports the standard
`Not supported on this platform` message, which is silent unless `display.showErrors` is on.

## Configuration

The module defines no keys of its own: `option.h` contains nothing but the shared `FFModuleArgs`.

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

A typo is the only thing the parser can reject: with `"display": { "showErrors": true }` the key
`them` prints `Cursor: Unknown JSON key them` and the module then runs normally with the typo
ignored. Without `showErrors` the same config is completely silent.

## Format string

Run `fastfetch -h cursor-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{theme}` | Cursor theme, after the suffix stripping described above |
| `{size}` | Cursor size, empty when the backend did not report one |

Neither variable exists in the key format: `ffPrintLogoAndKey()` parses `key` with `{index}`,
`{icon}` and `{module-name}` only, so `"key": "{theme}"` prints a literal `{theme}`. Two further
details of the shared format engine matter here:

- **An unknown placeholder is copied verbatim, conditional syntax included.** A key of
  `"[{?index}I{index}{?}][{?theme}T{theme}{?}]"` prints `[][{?theme}T{theme}{?}]`.
- **A conditional treats an empty string as unset.** `formatArgSet()` counts a string argument as
  set only when its length is non-zero, so `{?size}…{?}` stays silent for a backend that reported no
  size. Use it to build a size suffix that disappears instead of printing ` ()`.

The variables come from the same `FFCursorResult` the default line uses, so `{theme}` already has
the `cursors` suffix removed — unlike the JSON field of the same name, see below.

## JSON output

```jsonc
[
    {
        "type": "Cursor",
        "result": {
            "theme": "Windows Default",
            "size": "32"
        }
    }
]
```

Both fields are always present and both are strings — the size is *not* a number, which keeps the
`0`-means-unset convention representable. A detector error produces
`{ "type": "Cursor", "error": "…" }` with no `result` at all.

## Examples

```jsonc
// The default line, rebuilt by hand
{ "type": "cursor", "format": "{theme}{?size} ({size}px){?}" }
```

```jsonc
// The size suffix disappears entirely when the backend has no size to report
{ "type": "cursor", "format": "{theme}{?size} at {size}{?}" }
```

## Pitfalls

- **The JSON theme is not post-processed.** `ffPrintCursor()` strips a trailing `cursors`/`cursor`
  and falls back to `default`, but `ffGenerateCursorJsonResult()` writes the raw detector string.
  On a Linux desktop reporting `Adwaita-cursors` the text output says `Adwaita` while `--format json`
  says `Adwaita-cursors`, so the two paths disagree on the same machine even though they call the
  same detector.
- **`size: "0"` is hidden in the text output but kept in JSON.** The module suppresses the ` (0px)`
  suffix and prints only the theme, while the JSON still carries `"size": "0"`. A config that
  distinguishes "no size" from "size unknown" must therefore read the JSON, not the formatted line.
- **On Windows the theme is whatever Windows stores, which is localized.** The value is the default
  value of `HKCU\Control Panel\Cursors`, so an English installation reports `Windows Default` and a
  Chinese one reports `Windows 默认`; the module never translates it. Because the suffix stripping
  looks for the literal words `cursor` and `cursors`, a localized name is left alone.
- **On Windows the size is read only if the theme was read first.** `CursorBaseSize` is queried
  inside the successful-theme branch, so a machine where the theme value is missing reports no size
  either — even if the size value is present.
- **On Windows 8.1 there is no size at all.** `CursorBaseSize` was introduced later; the lookup
  fails, the size stays empty and the line loses its ` (Npx)` part. This is expected, not a defect.
- **On Linux an unsupported session aborts instead of falling back.** A WSLg session
  (`WSLg uses native windows cursor`) and a TTY session (`Cursor isn't supported in TTY`) report an
  error rather than trying the file-based paths, so a cursor that *is* configured in `~/.Xresources`
  is not found from a TTY.
- **On Linux the fallback chain is a fixed priority order, not a merge.** Qt/GTK settings beat
  `XCURSOR_THEME`, which beats `~/.icons/default/index.theme`, which beats `~/.Xresources`, which
  beats the XDG data directory. The first source that yields a theme wins outright; nothing combines
  a theme from one source with a size from another. On KDE Plasma and LXQt the module always takes
  the desktop-specific path (`kcminputrc`, `lxqt/session.conf`) and never reaches the chain.
- **On Linux the theme and the size can come from different files.** Only
  `detectCursorFromXResources()` also fills the size; the `index.theme` paths fill the theme alone.
  A desktop with `Xcursor.theme` in `~/.Xresources` and a size only in `XCURSOR_SIZE` prints the
  theme without a size.
- **`ffPrintError()` writes to stdout.** With `display.showErrors: true` an error line is printed
  before the JSON array, so the run is no longer parseable. Keep `showErrors` off in anything that
  pipes the JSON output.

## Implementation

`ffPrintCursor()` and `ffGenerateCursorJsonResult()` each call `ffDetectCursor()` themselves and
apply different post-processing to the result — the print path normalises the theme and hides a zero
size, the JSON path writes the strings through. The detection layer is not registered in the
`FFcache` layer, so a `--dynamic-interval` run re-detects on every round.

### Windows

`HKCU\Control Panel\Cursors` is opened for reading. Its default value (read with a `nullptr` name)
becomes `theme`, and the `CursorBaseSize` DWORD becomes `size` via `ffStrbufAppendUInt()`. The two
reads are nested, so a missing theme suppresses the size read as well.

### Linux, Android, FreeBSD, NetBSD, OpenBSD, Solaris, GNU/Hurd

The display-server result decides the branch:

1. `wmPrettyName == WSLg` → error, no further probing.
2. `wmProtocolName` equals `tty` (case-insensitive) → error.
3. `dePrettyName == Plasma` → `kcminputrc`, keys `cursorTheme =` / `cursorSize =`, defaults
   `Breeze` / `24`.
4. `dePrettyName == LXQt` → `lxqt/session.conf`, keys `cursor_theme =` / `cursor_size =`, defaults
   `Adwaita` / `24`.
5. `wmPrettyName == Hyprland` and `HYPRCURSOR_THEME` is set → that variable plus
   `HYPRCURSOR_SIZE`.
6. Otherwise the chain described above: GTK4, then GTK3, then GTK2 (the first one whose cursor
   setting is non-empty), `XCURSOR_THEME` + `XCURSOR_SIZE`, `Inherits =` in
   `~/.icons/default/index.theme`, `Xcursor.theme :` / `Xcursor.size :` in `~/.Xresources`, and
   finally `Inherits =` in the XDG data directory's `icons/default/index.theme`.

A branch that finds a file but no usable value reports `Couldn't find cursor in <file>`; when the
whole chain comes up empty the message is `Couldn't find cursor`. The GTK/Qt settings are themselves
cached (`gtk_qt` is in the `FFcache` layer), so the same round-trip does not re-launch the GTK
probes for every module that needs them.

### macOS

The theme is synthesised, not read from a cursor-theme directory: `cursorFill` and `cursorOutline`
from the `com.apple.universalaccess` preferences domain are formatted as
`Fill - <colour>, Outline - <colour>` (falling back to `Black` and `White` when a value is missing
or is not a dictionary with `red`/`green`/`blue`/`alpha`). Twelve exact colours are named —
`Black`, `Blue`, `Brown`, `Cyan`, `Green`, `Magenta`, `Orange`, `Purple`, `Red`, `Yellow`, `White`,
`Transparent` — and anything else is written as `#RRGGBBAA`. Because the string never contains the
word `cursor`, the module's suffix stripping is a no-op here.

The size is `mouseDriverCursorSize × 32`, rounded to the nearest integer, with `32` used when the
preference is absent. The multiplication is why a macOS cursor size looks large compared to the
point size shown in System Settings.
