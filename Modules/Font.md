# Font

> Print system font names

| | |
|---|---|
| Module type | `font` |
| Default order | 26 (only used by `--gen-config`) |
| Module source | `src/modules/font/font.c` |
| Detection source | `src/detection/font/` |

Prints the fonts the desktop actually uses for its own user interface — **not** the list of installed
font families:

```
Font: Microsoft YaHei UI (24pt) [Caption / Menu / Message / Status]
```

The value is a combination of up to four font slots. Slots whose font string is identical to the
previous slot are merged into one label group, which is why the sample above shows a single name
followed by four labels: all four shell fonts on that machine are the same typeface. When the slots
differ, each group starts a new `Name [Label` part, separated by `, `:

```
Font: Consolas (9pt) [Caption], Segoe UI (9pt) [Menu / Message / Status]
```

Labels that are adjacent in the group list are merged even when they are not a prefix of each other
— `[Caption / Message]` just means slots 0 and 2 hold the same string and slot 1 holds a different
one, in that order. What each slot *means* depends on the platform:

| Slot | Linux, *BSD | macOS | Windows | Haiku |
|---|---|---|---|---|
| 1 | Qt application font | System font | `CaptionFont` | Plain font |
| 2 | GTK2 font | User font | `MenuFont` | Menu font |
| 3 | GTK3 font | System monospace font | `MessageFont` | Bold font |
| 4 | GTK4 font | User monospace font | `StatusFont` | Monospace font |

A platform that fills none of the slots leaves them all empty, and the format variables for those
slots render as empty strings.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `font_linux.c` | Qt settings plus the GTK2/3/4 settings |
| Android | `font_linux.c` | Same code; needs a display server |
| FreeBSD | `font_linux.c` | Same code |
| NetBSD | `font_linux.c` | Same code |
| OpenBSD | `font_linux.c` | Same code |
| Solaris / illumos | `font_linux.c` | Same code |
| Haiku | `font_haiku.cpp` | `BFont` / `get_menu_info()` |
| GNU/Hurd | `font_linux.c` | Same code as Linux |
| macOS | `font_apple.m` | `NSFont` |
| Windows | `font_windows.c` | Registry, `HKCU\Control Panel\Desktop\WindowMetrics` |

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

There is no option to choose *which* desktop's fonts are read, and no option to limit the output to
one slot — use `format` for that.

## Format string

Run `fastfetch -h font-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{font1}` | Slot 1 (see the table above) |
| `{font2}` | Slot 2 |
| `{font3}` | Slot 3 |
| `{font4}` | Slot 4 |
| `{combined}` | The merged, labelled string that the default line prints |

None of the five is available in the key format: `ffPrintLogoAndKey()` parses `key` with `{index}`,
`{icon}` and `{module-name}` only, so `"key": "{font1}"` prints a literal `{font1}`. Two further
details of the shared format engine matter here:

- **An unknown placeholder is copied verbatim, conditional syntax included.** A key of
  `"[{?index}I{index}{?}][{?font1}F{font1}{?}]"` prints `[][{?font1}F{font1}{?}]`.
- **A conditional treats an empty string as unset.** `formatArgSet()` counts a string argument as set
  only when its length is non-zero, so `{?font3}…{?}` stays silent on a platform that leaves slot 3
  empty — but it also stays silent when a slot *is* used and simply holds an empty name.

`{combined}` and the default output are the same string; using `format` only changes how the line is
arranged, not what is detected.

## JSON output

```jsonc
[
    {
        "type": "Font",
        "result": {
            "display": "Microsoft YaHei UI (24pt) [Caption / Menu / Message / Status]",
            "fonts": [
                "Microsoft YaHei UI (24pt)",
                "Microsoft YaHei UI (24pt)",
                "Microsoft YaHei UI (24pt)",
                "Microsoft YaHei UI (24pt)"
            ]
        }
    }
]
```

`fonts` always has exactly four entries, in slot order, with `""` for a slot the backend did not
fill. `display` is the same string the default line prints. A detector error produces
`{ "type": "Font", "error": "…" }` with no `result`.

## Examples

```jsonc
// Just the Qt / GTK value the desktop is configured with
{ "type": "font", "format": "{font1}" }
```

```jsonc
// Keep the labelless form: the four slots joined by " | "
{ "type": "font", "format": "{font1} | {font2} | {font3} | {font4}" }
```

```jsonc
// The four slots joined by ", ", with each comma bound to the slot that follows it —
// a slot that is empty drops its own comma, but an empty *first* slot would leave a leading ", "
{ "type": "font", "format": "{?font1}{font1}{?}{?font2}, {font2}{?}{?font3}, {font3}{?}{?font4}, {font4}{?}" }
```

## Pitfalls

- **This is not a list of installed fonts.** Each backend reads one *configured* font per slot: the
  shell's `LOGFONT` values on Windows, the Qt and GTK settings on Linux, `NSFont` and
  `get_menu_info()` on Haiku. A machine with 400 installed families still prints at most four, and
  changing the desktop theme changes the output.
- **On macOS the two monospace fonts never reach the default output.** `generateString()` in
  `font_apple.m` only appends slots 1 and 2 (`System`, `User`) to `display`; the monospace slots are
  filled for `{font3}` / `{font4}` and for the JSON `fonts` array, but `{combined}` and the default
  line silently omit them. On Linux, Windows and Haiku all four slots are included.
- **On macOS only the family name is printed, never the size.** The detector asks
  `NSFont …OfSize:12` and then keeps `familyName` alone, so the ` (Npt)` part that the other
  platforms add is absent and the printed name does not tell you the configured size.
- **On Windows a slot whose registry value is missing or the wrong length still gets printed.** The
  detector skips a `LOGFONTW` whose buffer length is not `sizeof(LOGFONTW)` and leaves that slot
  empty, but the display string is assembled without checking for empty names — the label is emitted
  anyway, producing an entry such as ` [Caption]` with nothing before the bracket. The Linux and
  macOS builders do check for a non-empty string, so this shape is Windows-only.
- **On Windows the size is `-lfHeight` in points and only appears when `lfHeight` is negative.**
  `lfHeight` is the *cell* height in logical units when it is zero or positive, and in that case the
  module appends nothing — the same typeface can print as `Consolas (9pt)` on one machine and
  `Consolas` on another, depending on how the shell stored the value.
- **On Linux the size unit is guessed.** The shared `FFfont` helper appends `pt` when the configured
  size string ends in neither `pt` nor `px`, so a bare `11` becomes `11pt` even when the desktop
  meant pixels. A desktop that stores `11px` keeps it as written.
- **On Linux a TTY session is an error, not an empty result.** The module reports
  `Font isn't supported in TTY` (silent unless `display.showErrors` is on) and prints nothing; it
  does not fall back to the X resources or the config files.
- **On Linux the GTK half is a merge, so `{font2}` can be a whole group.** Slots 2–4 are formatted
  through the shared `ffParseGTK()` helper, which decides between `[GTK2/3/4]`, `[GTK2/3],
  [GTK4]`-style labels and a plain `[GTK4]` depending on which of the three agree. Reading `{font2}`
  therefore gives you a labelled fragment, not necessarily a bare font name.
- **Fonts are re-read on every `--dynamic-interval` round.** The Qt and GTK detection layers are
  registered in the `FFcache` layer, whose entries are dropped at each round boundary, and the
  Windows registry read is repeated every round as well.
- **`ffPrintError()` writes to stdout.** With `display.showErrors: true` an error line is printed
  before the JSON array, so the run is no longer parseable. Keep `showErrors` off in anything that
  pipes the JSON output.

## Implementation

`ffPrintFont()` and `ffGenerateFontJsonResult()` both call `ffDetectFont()` on a four-element
`FFFontResult` and then read the same strings. The text path re-formats them itself, the JSON path
writes `display` plus the four slots. The result is destroyed in both paths.

### Shared

The Linux, *BSD and Haiku backends go through `src/common/font.h`: `ffFontInitQt()`,
`ffFontInitPango()`, `ffFontInitValues()` and friends parse the raw configuration string into a
`name`, an optional `size` and a style list, and `ffFontInitPretty()` assembles
`Name (size, styles)` — with `pt` appended when the size carries no unit, and with `default` standing
in for an empty name when a size or style is present. The `pretty` string is what ends up in the four
slots.

### Windows

`HKCU\Control Panel\Desktop\WindowMetrics` is opened for reading and four `LOGFONTW` values are read
in one `ffRegReadValues()` call: `CaptionFont`, `MenuFont`, `MessageFont`, `StatusFont`. Each value
whose length equals `sizeof(LOGFONTW)` contributes `lfFaceName`, plus ` (Npt)` when `lfHeight` is
negative. The grouping is then done directly on the four strings. A failure to open the key or to
read the four values is reported as an error, which makes every slot empty.

### Linux, Android, FreeBSD, NetBSD, OpenBSD, Solaris, GNU/Hurd

The display-server result is consulted first: a TTY session aborts. Otherwise the Qt settings are
turned into slot 1 and the GTK2, GTK3 and GTK4 settings into slots 2–4, each through
`ffFontInitPango()`. The `display` string is `"<slot1> [Qt], "` followed by the GTK merge — the
trailing separator is only added when at least one GTK slot is non-empty.

### macOS

`NSFont systemFontOfSize:12`, `NSFont userFontOfSize:12`, `NSFont monospacedSystemFontOfSize:12
weight:400` and `NSFont userFixedPitchFontOfSize:12` supply the four slots' `familyName` values. The
monospace lookup is behind a compile-time `MAC_OS_X_VERSION_10_15` check and contributes an empty
string when the SDK predates it. `display` is built from slots 1 and 2 only.

### Haiku

`be_plain_font` / `get_menu_info()` / `be_bold_font` / `be_fixed_font` supply the four slots, each as
`Family Style (Npt)`; a `BApplication` is instantiated first because querying `app_server` needs a
valid application object. The grouping matches the Windows one.
