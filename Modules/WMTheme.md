# WMTheme

> Print the current window manager theme

| | |
|---|---|
| Module type | `wmtheme` |
| Default order | 23 (only used by `--gen-config`) |
| Module source | `src/modules/wmtheme/wmtheme.c` |
| Detection source | `src/detection/wmtheme/` |

Prints the theme of the window manager that is currently running. The result is one string, not two
parts like `Modules/Theme`.

```
WM Theme: Breeze
WM Theme: Adwaita
WM Theme: Custom - Blue (System: Dark, Apps: Dark)
WM Theme: Red (Dark)
```

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `wmtheme_linux.c` | Per-WM: config files, GSettings / DConf, XFConf, GTK theme as a fallback |
| Android | `wmtheme_linux.c` | Same file |
| FreeBSD / NetBSD / OpenBSD / DragonFly | `wmtheme_linux.c` | Same file |
| Solaris / illumos | `wmtheme_linux.c` | Same file |
| GNU/Hurd | `wmtheme_linux.c` | Same file |
| macOS | `wmtheme_apple.c` | Accent colour plus the interface style |
| Windows | `wmtheme_windows.c` | Theme file name, accent colour and the light/dark preference |
| Haiku | `wmtheme_haiku.cpp` | `DecorInfoUtility::CurrentDecorator()` |

Every platform has an implementation.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | `WM Theme` | Module key. A single space hides the key and the separator |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

There are no module-specific options.

## Format string

Run `fastfetch -h wmtheme-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{result}` | WM theme |

`{result}` is not marked `*` in the help output, so it is not available in the key format. It is the
only variable the module has — the default output and `{result}` are the same string.

## JSON output

```jsonc
[
    {
        "type": "WMTheme",
        "result": "Custom - Blue (System: Dark, Apps: Dark)"
    }
]
```

- `result` is a **plain string**, not an object and not an array — the whole theme is one value.
- On failure the object carries `error` instead and has no `result`.

## Examples

```jsonc
// Hide the module unless a theme was found (an error is invisible by default anyway)
{ "type": "wmtheme", "format": "{?result}{result}{?}" }
```

## Pitfalls

- **The result is a single string, so nothing is structured.** There is no way to ask for "just the
  accent colour" on Windows or "just the style" on macOS through the format string — the module
  composes one string and hands it over.
- **On Windows the value is not a window-manager theme at all.** It is the file name of the active
  `.theme` file (with the extension stripped and the first letter capitalised), optionally followed
  by ` - <accent name or #RRGGBB>`, optionally followed by ` (System: Light|Dark, Apps: Light|Dark)`
  from `Themes\Personalize`. Each part is skipped when its registry value is missing, so
  `Custom`, `Custom - Blue` and `Custom - Blue (System: Dark, Apps: Dark)` are all valid shapes.
- **The Windows accent colour is a fixed name table, and a hit is a guess.** The registry value is
  an RGB triple that is looked up in a hard-coded list of about 50 Windows accent colours; a miss
  falls back to `#RRGGBB`. The same colour can therefore be named on one machine and printed as
  hex on another if the list drifts from the installed Windows version.
- **Windows reports the light/dark preference for two separate things.** `SystemUsesLightTheme`
  and `AppsUseLightTheme` are printed together, because they can disagree; a single "is dark mode
  on" answer does not exist there.
- **`WM Theme needs successful WM detection` is the first failure mode on Unix.** The module starts
  from `ffDetectWM()`; without a window manager name it errors out immediately. A TTY session
  therefore reports this error rather than a theme.
- **Only a fixed list of window managers is supported on Unix.** `kwin` (config file, defaulting to
  `Breeze`), `cosmic-comp`, `xfwm4` (and `labwc` under XFCE), `mutter` (GNOME only — other desktops
  fall back to the GTK theme), `muffin`, `marco`, `openbox` and `enlightenment` have branches;
  anything else reports `Unknown WM: <name>`. Notably `i3`, `sway`, `hyprland` and `bspwm` are not
  in the list.
- **The Plasma-specific prefixes are stripped.** `kwinrc`'s `theme` value often carries a
  `qml_` or `svg__` prefix that Plasma adds internally; both are removed, and the value after the
  last prefix wins.
- **`muffin` can return two names in one string.** When Cinnamon reports both
  `org.cinnamon.theme::name` and `org.cinnamon.desktop.wm.preferences::theme`, the result is
  `<name> (<theme>)`; with only one of them set, that one is printed alone.
- **COSMIC comp's value is assembled from several files, and includes an accent colour.** The
  variant comes from `is_dark`, the name from `CosmicTheme.<Variant>/v1/name` (falling back to
  `Dark` / `Light`), and the accent from a Rust-debug-formatted struct that is parsed for
  `red` / `green` / `blue` / `alpha` — printed as `#RRGGBB` when opaque and `#RRGGBBAA` otherwise.
- **`openbox` parses XML by hand.** The `<theme><name>` node is located with `strstr()`, so a
  `<name>` that appears after `</theme>` is rejected, and a missing node yields
  `Couldn't find theme node in "…"`. The config file is `openbox/rc.xml`, or `openbox/lxqt-rc.xml`
  / `openbox/lxde-rc.xml` under LXQt / LXDE.
- **Errors are invisible by default.** Every failure string goes through `ffPrintError()` and needs
  `display.showErrors` to be `true`.

## Implementation

`ffDetectWmTheme()` takes a single `FFstrbuf*` that is either the theme or the error message —
hence the name `themeOrError` — and returns a `bool`. `ffPrintWMTheme()` prints the buffer on
success and passes the same buffer through `ffPrintError()` on failure; the JSON path writes it to
`error` or `result` accordingly.

### Unix

`ffDetectWmTheme()` dispatches on the display server's pretty WM name. Four mechanisms are used:

- **Config files** — kwin (`kwinrc`, `theme =`, default `Breeze`), openbox (`rc.xml` variants),
  COSMIC (`~/.config/cosmic/…`).
- **GSettings / DConf** — mutter's user-theme extension, muffin (two keys), marco
  (`org.mate.Marco.general::theme`).
- **XFConf** — xfwm4 (`/general/theme`).
- **GTK theme as a fallback** — `detectGTKThemeAsWMTheme()` tries GTK4, then GTK3, then GTK2, and is
  the whole answer for `mutter` on a non-GNOME desktop and for `enlightenment`.

### macOS

Two `CFPreferencesCopyAppValue()` reads against `kCFPreferencesAnyApplication` — the same values the
system exposes through `defaults`:

- `AppleAccentColor` maps to a name (`-1` Graphite, `0`–`6` Red / Orange / Yellow / Green / Blue /
  Purple / Pink, anything else `Unknown`), and an **unset** key means `Multicolor`, which is the
  default on macOS.
- `AppleInterfaceStyle` is appended in parentheses; when it is unset the result is `(Light)`, since
  macOS only stores the key while dark mode is active.

The preferences are read through `cfprefsd` rather than the raw plist file, so the answer matches
what the system itself sees.

### Windows

Three registry reads, each optional: `Themes::CurrentTheme` for the file name,
`DWM::AccentColor` (a BGR value that is byte-swapped to RGB) with `DWM::ColorizationColor` as the
fallback, and `Themes\Personalize::SystemUsesLightTheme` / `AppsUseLightTheme`. When nothing at all
was found the module reports `Failed to find current theme`.

### Haiku

A `BApplication` is created — required before the app_server can be queried — and
`DecorInfoUtility::CurrentDecorator()` supplies the name.
