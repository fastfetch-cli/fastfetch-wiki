# Theme

> Print the current desktop environment theme

| | |
|---|---|
| Module type | `theme` |
| Default order | 24 (only used by `--gen-config`) |
| Module source | `src/modules/theme/theme.c` |
| Detection source | `src/detection/theme/` |

Prints the theme the desktop is currently using. Two independent parts are collected and joined
with `, ` in the default output.

```
Theme: Breeze (Dark) [Qt], Adwaita [GTK2/3/4]
Theme: Fluent
Theme: Aqua
```

`{theme1}` is the window-manager / Qt side, `{theme2}` the GTK side. Only one of them is filled on
most systems — on Windows and macOS the whole answer sits in `{theme1}`, and a GTK-only desktop
fills `{theme2}` alone.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `theme_linux.c` | Qt/Plasma from the Qt detector, GTK2/3/4 from the GTK detector |
| Android | `theme_linux.c` | Same file |
| FreeBSD / NetBSD / OpenBSD / DragonFly | `theme_linux.c` | Same file |
| Solaris / illumos | `theme_linux.c` | Same file |
| GNU/Hurd | `theme_linux.c` | Same file |
| macOS | `theme_apple.c` | Derived from the reported macOS version |
| Windows | `theme_windows.c` | Derived from the reported Windows version and the detected window manager |
| Haiku | `theme_nosupport.c` | Stub: always reports `Not supported on this platform` |

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | `Theme` | Module key. A single space hides the key and the separator |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

There are no module-specific options.

## Format string

Run `fastfetch -h theme-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{theme1}` | Theme part 1 — Qt / window-manager side |
| `{theme2}` | Theme part 2 — GTK side |

Neither is marked `*` in the help output, so neither is available in the key format.

Both are empty strings when nothing was found for that side, so a format that prints them
unconditionally can produce empty brackets:

```jsonc
{ "type": "theme", "format": "[{theme1}][{theme2}]" }
```

```
Theme: [Fluent][]
```

## JSON output

```jsonc
[
    {
        "type": "Theme",
        "result": {
            "theme1": "Fluent",
            "theme2": ""
        }
    }
]
```

- Both keys are always present. An unfilled side is `""`, never `null` or absent.
- On failure the object carries `error` instead and has no `result`.
- The key order is `theme1`, `theme2`.

## Examples

```jsonc
// Only the GTK theme, and hide the module when there is none
{ "type": "theme", "format": "{?theme2}{theme2}{?}" }
```

```jsonc
// A compact single-value form for a status bar
{ "type": "theme", "format": "{theme1}{?theme2} / {theme2}{?}" }
```

## Pitfalls

- **A successful detection can still print nothing.** Neither `theme_windows.c` nor
  `theme_apple.c` ever returns an error: they fill what they can and return `nullptr`. On Windows,
  if the detected window manager is neither `dwm.exe` nor `explorer.exe`, both parts stay empty and
  the default output is a bare `Theme:`. On macOS, an OS version that does not start with a digit
  has the same effect.
- **On Windows the theme name is not read from a theme file's contents.** It is the file name of
  `HKCU\…\Themes` → `CurrentTheme` with the extension stripped and the first letter capitalised —
  so a renamed `.theme` file produces that name, and the built-in themes show up as `Fluent`,
  `Metro` or `Aero` based on the reported Windows version instead.
- **The Windows version mapping has two separate ladders.** A version above `1000` is treated as a
  Windows Server release (`>= 2016` → Fluent, `>= 2012` → Metro, else Aero); anything else is a
  client release (`>= 10` → Fluent, `>= 8` → Metro, else Aero). The version comes from the `OS`
  module, so a spoofed or unusual version string changes this answer.
- **macOS reports a fixed name per version range, not the actual appearance.** `> 15` is
  `Liquid Glass`, `< 10` is `Platinum`, and everything in between is `Aqua`. The value says nothing
  about light or dark mode — that is the `WMTheme` module's job.
- **A TTY reports an error, not an empty theme.** On Unix the module returns
  `Theme isn't supported in TTY` when the display server is the TTY protocol, and `No themes found`
  when Qt, GTK2, GTK3 and GTK4 all came back empty. Both go through `ffPrintError()` and are
  therefore invisible unless `display.showErrors` is `true`.
- **The Linux side merges three GTK versions into one string.** `ffParseGTK()` compares the GTK2,
  GTK3 and GTK4 themes case-insensitively and collapses the ones that agree, annotating the result
  as `[GTK2/3/4]`, `[GTK2/3], … [GTK4]`, `[GTK2], … [GTK3/4]` or `[GTK2], … [GTK3], … [GTK4]`.
  `{theme2}` is therefore not a single theme name.
- **The Qt part is annotated too.** When a Plasma widget style or colour scheme was found, `[Qt]`
  is appended to `{theme1}`; a colour scheme that starts with the widget style has that prefix
  removed before it is put in parentheses (`Breeze` + `BreezeDark` → `Breeze (Dark)`).
- **The two parts are independent.** A machine can report a Qt theme and no GTK theme, or the other
  way round; `{theme1}` being empty does not mean detection failed.

## Implementation

`ffDetectTheme()` fills an `FFThemeResult` — two `FFstrbuf`s, `theme1` and `theme2` — and returns a
`const char*` error (or `nullptr`). `ffPrintTheme()` joins the two with `, ` when both are set, and
`ffGenerateThemeJsonResult()` writes both keys.

### Linux

`ffConnectDisplayServer()` supplies the display-server result; the TTY protocol short-circuits with
an error. Then `ffDetectQt()`, `ffDetectGTK2()`, `ffDetectGTK3()` and `ffDetectGTK4()` are called —
if all five values (widget style, colour scheme and the three themes) are empty, the module reports
`No themes found`. Otherwise `ffParseGTK()` builds `theme2` from the GTK themes and `theme1` is
built from the Plasma widget style and colour scheme, with `[Qt]` appended when either is present.

### macOS

`ffDetectTheme()` reads the `OS` module's version and parses its leading integer with
`strtoul()`. When the parse consumed at least one character the version is classified; otherwise
nothing is set. There is no plist or API query behind it.

### Windows

`ffDetectTheme()` compares the display server's `wmProcessName`: `dwm.exe` selects the
version-based name, `explorer.exe` yields `Basic`, and anything else leaves both parts empty. The
Windows Server / client split is the `ver > 1000` test described above.
