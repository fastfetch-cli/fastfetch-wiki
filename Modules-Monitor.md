# Monitor

> Same as Display module, but with a different default output format

| | |
|---|---|
| Module type | `monitor` |
| Default order | 19 (only used by `--gen-config`) |
| Module source | `src/modules/monitor/monitor.c` |
| Detection source | `src/detection/displayserver/` |

Prints one line per connected display, in a layout built for panels rather than desktops: the native
resolution, the refresh rate, and the physical size with the diagonal in inches and the pixel
density.

```
Monitor (HDMI-1): 1920x1080 px @ 60 Hz - 521x293 mm (23.99 inches, 91.79 ppi)
```

`Monitor` is the **same detection** as [Display](Modules-Display) — it calls the same
`ffConnectDisplayServer()` and walks the same display list — with a different default line and a
different set of format variables. Everything after the resolution is conditional:

- ` @ <n> Hz` is printed only when the display reported a refresh rate.
- ` - <w>x<h> mm (<inch> inches, <ppi> ppi)` is printed only when the physical size is known, and
  the value is the panel's EDID size in millimetres.
- ` [HDR Compatible]` is printed when the display supports HDR, **or** when HDR is enabled.

The key is `Monitor (<name>)`, or a bare `Monitor` when the display has no name. Unlike `Display`
there is no `(<n>)` fallback and no per-display numbering: two nameless displays both print
`Monitor`.

## Platform support

The module layer is platform independent — `monitor.c` only formats what the shared
`displayserver` subsystem returns. Detection is therefore identical to
[Display](Modules-Display#platform-support): the `displayserver` subsystem has a real implementation
on all ten platforms (Wayland / XCB / Xlib / DRM / sysfs on Linux and the BSDs, CoreGraphics on
macOS, GDI plus `DisplayConfigGetDeviceInfo` on Windows, `BScreen` on Haiku, and the `cmd` /
`dumpsys` / `getprop` chain on Android).

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | module name + display name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

There are **no** module-specific options — only the standard module arguments. In particular the
three options that shape `Display`'s output are **not** accepted here: `compactType`,
`preciseRefreshRate` and `order` are rejected with `Unknown JSON key …`, because
`ffParseMonitorJsonObject()` only forwards the module arguments. See the pitfalls.

Inside `key` the available variables are `{index}`, `{name}`, `{icon}` and `{module-name}`; `{index}`
is `0` when there is only one display.

## Format string

Run `fastfetch -h monitor-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{name}` | Display name * |
| `{width}` | Native resolution width in pixels |
| `{height}` | Native resolution height in pixels |
| `{physical-width}` | Physical width in millimetres |
| `{physical-height}` | Physical height in millimetres |
| `{inch}` | Physical diagonal in inches |
| `{ppi}` | Pixels per inch |
| `{manufacture-year}` | Year of manufacture, `0` when unknown |
| `{manufacture-week}` | Week of manufacture, `0` when unknown |
| `{serial}` | Serial number, empty when unknown |
| `{refresh-rate}` | Refresh rate in Hz |
| `{hdr-compatible}` | `true` when the display supports HDR, whether or not it is on |

`{name}` is the only variable marked `*`, i.e. also usable in the key format. The list is **not** a
subset of `Display`'s: `{width}` / `{height}` look the same, but `Monitor` has no `{scaled-width}`,
`{type}`, `{rotation}`, `{is-primary}`, `{bit-depth}`, `{platform-api}` or `{dpi}`, and adds nothing
of its own. `{width}` and `{height}` are the same native mode both modules report.

`{inch}`, `{ppi}` and `{refresh-rate}` are floating-point values, so they are formatted with
`display.fraction` — two decimals by default (`31.48`, `139.94`, `165.00`). `{manufacture-year}` and
`{manufacture-week}` are integers, and `{serial}` is a string.

## JSON output

The module has **no** JSON result of its own — it always emits the alias error:

```jsonc
[
    {
        "type": "Monitor",
        "error": "Monitor module is an alias of Display module"
    }
]
```

`ffGenerateMonitorJsonResult()` writes the `error` string and returns `false` without touching the
display list. Use the `display` module when a structured result is needed; the two read the same
detection, so `{ "type": "display" }` gives exactly the data this module formats.

## Examples

```jsonc
// The default line, but only the native resolution
{ "type": "monitor", "format": "{width}x{height}" }
```

```jsonc
// Panel report: size, density and EDID metadata
{ "type": "monitor", "format": "{name}: {inch}in {ppi}ppi {manufacture-year}-W{manufacture-week} {serial}" }
```

```jsonc
// Rename the key and print the refresh rate next to the resolution
{ "type": "monitor", "key": "Panel {index}", "format": "{width}x{height} @ {refresh-rate} Hz" }
```

## Pitfalls

- **The JSON output is an error object, not a result.** `Monitor` is declared as an alias of
  `Display`, so `--format json` yields `{"type":"Monitor","error":"Monitor module is an alias of
  Display module"}` and nothing else. A JSON consumer that wants display data has to ask for
  `display`.
- **`compactType`, `preciseRefreshRate` and `order` are rejected.** They belong to the `display`
  module. Setting any of them on a `monitor` entry prints `Unknown JSON key <name>` (and with
  `display.showErrors` off the rest of the run is unaffected but the line is still formatted with the
  defaults). The `Monitor` layout is fixed: there is no way to collapse it to one line.
- **The refresh rate is rendered twice, differently.** The default line prints it with `%g` after
  rounding to three decimals, so `165.0` Hz shows as `165 Hz`; the `{refresh-rate}` variable is a
  double formatted with `display.fraction`, so the same panel shows `165.00`. A format string that
  reproduces the default line has to round it itself.
- **`{inch}` here is not `Display`'s `{inch}`.** The `display` module rounds its `{inch}` to a whole
  number; `Monitor` prints the real value with `display.fraction` (`31.48`, not `31`). The default
  line uses two decimals explicitly (`%.2f inches, %.2f ppi`).
- **The physical block vanishes together, but `{ppi}` has no guard of its own.** The ` - … mm (… inch
  … ppi)` part is only printed when the diagonal is greater than zero, which comes from the EDID
  physical size. `{ppi}` is computed as `sqrt(width² + height²) / inch`, so it follows the same value
  and is not meaningful when no physical size was reported.
- **`[HDR Compatible]` is not the same marker as `Display`'s `[HDR]`.** The `monitor` layout prints
  the marker when the status is `Supported` **or** `Enabled`, so a panel that can do HDR but has it
  switched off still shows `[HDR Compatible]`. `{hdr-compatible}` is `true` in both cases as well.
- **`order` set on `Display` does not affect `Monitor`.** `ffConnectDisplayServer()` returns a
  process-wide static that `Display` sorts only for its own output, so `Monitor` prints the displays
  in detection order unless the machine reports them sorted. Set the layout on each module
  separately — but note that `Monitor` cannot sort at all.
- **The key is never numbered.** With several displays every line carries its own
  `Monitor (<name>)`; there is no `Monitor 1:` / `Monitor 2:`. Displays without a name are
  indistinguishable in the output.
- **`serial` can look like a hex word.** EDID serials are frequently a hexadecimal token rather than a
  text string; it is reported verbatim as a string.

## Implementation

`ffPrintMonitor()` calls `ffConnectDisplayServer()` and iterates `result->displays`. For each display
it derives the diagonal from the EDID size and the density from the resolution:

```c
double inch = sqrt(physicalWidth² + physicalHeight²) / 25.4;
double ppi  = sqrt(width² + height²) / inch;
bool hdrCompatible = hdrStatus == FF_DISPLAY_HDR_STATUS_SUPPORTED
                  || hdrStatus == FF_DISPLAY_HDR_STATUS_ENABLED;
```

It then builds the key (`Monitor`, plus ` (<name>)` when the display has one, or the parsed `key`
format) and prints either the fixed layout or the custom `format`. Because the detection result is a
singleton shared with every other display module, the strings are deliberately not destroyed.

There is no detection code in the module directory: everything comes from
`src/detection/displayserver/`, whose per-platform behaviour is described in
[Display](Modules-Display#implementation). One consequence worth repeating here is that
`ffdsAppendDisplay()` drops any display with a zero width or height and forces a DPI of 96 when the
backend cannot report one, so a `Monitor` line always has a usable resolution.
