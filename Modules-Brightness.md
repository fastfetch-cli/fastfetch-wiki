# Brightness

> Print the current brightness level of your monitors

| | |
|---|---|
| Module type | `brightness` |
| Default order | 18 (only used by `--gen-config`) |
| Module source | `src/modules/brightness/brightness.c` |
| Detection source | `src/detection/brightness/` |

Prints one line per display with a brightness reading, as a percentage of that display's own
range, followed by a marker for the display type.

```
Brightness (Color LCD): 87% [Built-in]
Brightness (Sample Monitor): 100% [External]
```

The percentage is normalised from the device's native scale — `(current - min) / (max - min) * 100`
— so a panel that reports `0..1`, one that reports `0..100` and one whose backlight goes to `1200`
all end up on the same 0–100 scale. The raw values are still what `{current}`, `{min}` and `{max}`
expose, and they are **not** comparable between displays.

Unlike [Display](Modules-Display), this module reports a hardware state that has to be *queried*,
and there are only two ways to do it: the platform's own backlight API for built-in panels, and
DDC/CI over I2C for external ones. Which of the two answered is what the `[Built-in]`/`[External]`
marker reflects — it is a property of the code path, not a fact about the hardware.

## Platform support

| Platform | Implementation | Built-in panel | External panel |
|---|---|---|---|
| Linux | `brightness_linux.c` | `/sys/class/backlight/*` | DDC/CI via `libddcutil` (optional, `dlopen`ed) |
| Android | `brightness_nosupport.c` | – | – |
| FreeBSD | `brightness_bsd.c` | `/dev/backlight/backlightN` (FreeBSD 13+) | DDC/CI over `/dev/iicN` |
| NetBSD | `brightness_nbsd.c` | `hw.acpi.acpioutN.brightness` | – |
| OpenBSD | `brightness_obsd.c` | `/dev/ttyCN` via `wsdisplay` | – |
| Solaris/illumos | `brightness_nosupport.c` | – | – |
| Haiku | `brightness_haiku.cpp` | `BScreen::GetBrightness()` | – |
| GNU/Hurd | `brightness_nosupport.c` | – | – |
| macOS | `brightness_apple.c` | `DisplayServicesGetBrightness()` (private) | DDC/CI over IOKit I2C |
| Windows | `brightness_windows.c` | WMI `WmiMonitorBrightness` | DDC/CI via `gdi32!GetPhysicalMonitors` |

The three `_nosupport.c` platforms report `Not supported on this platform`.

`libddcutil` is a **runtime** dependency on Linux, not a build one: it is `dlopen`ed, and a system
without it still reports the built-in panel. The build switch `-DENABLE_DDCUTIL=ON` controls
whether the `dlopen` code is compiled in at all.

The module depends on the `displayserver` subsystem on every platform that has a real
implementation — the detection starts by calling `ffConnectDisplayServer()` to learn which displays
exist, and uses their ids and names to query and label each one.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | module name + display name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works, `""` prints none. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). Ignored by `compact`. |
| `condition` | object | – | Show the module only if the conditions match. |
| `percent` | object | `{ "green": 100, "yellow": 100, "type": 0 }` | Percentage rendering and colour thresholds. |
| `ddcciSleep` | integer or `null` | `10` | Delay in milliseconds inserted around a DDC/CI transaction. `null` disables DDC/CI entirely. |
| `compact` | boolean | `false` | Print all displays as bare percentages on one line. |

Inside `key` the available variables are `{index}`, `{name}`, `{icon}` and `{module-name}`.

### `percent`

`percent.type` is either a number or an array of strings — **not** a bare string. A string is
rejected before the module ever runs:

```
Error: usage: percent.type must be a number or an array of strings
```

| Form | Value | Meaning |
|---|---|---|
| number | `0` | Use `display.percent.type` (default) |
| number | `1` | Number only |
| number | `2` | Multicolour bar |
| number | `3` | Bar and number |
| number | `6` | Bar only |
| number | `9` | Coloured number (the global default) |
| number | `10` | Monochrome bar |
| array | `["num"]`, `["bar"]`, `["num-color"]`, `["bar-monochrome"]`, `["hide-others"]` | Same flags, as a set |

`green` and `yellow` are thresholds, and their order changes the meaning:

- `green <= yellow` — "higher is worse": `[0, green]` green, `(green, yellow]` yellow,
  `(yellow, 100]` red.
- `green > yellow` — "higher is better": `[green, 100]` green, `[yellow, green)` yellow,
  `[0, yellow)` red.

Brightness ships with `green = yellow = 100`, which is the first form and means **every reading
from 0 to 100 is green**. A dim screen is not treated as a problem. To flag a screen that is too
dark, invert the pair:

```jsonc
{ "type": "brightness", "percent": { "green": 20, "yellow": 80 } }
```

### `ddcciSleep`

The DDC/CI protocol needs a pause between the write and the read, and how the value is interpreted
differs per platform:

| Platform | Use of the value |
|---|---|
| Linux | `ddca_set_default_sleep_multiplier(ddcciSleep / 40.0)` — the default `10` becomes a multiplier of `0.25` |
| macOS, Apple Silicon | `ffTimeSleep(ddcciSleep)` between the write and the read |
| macOS, Intel | `IOI2CRequest.minReplyDelay = ddcciSleep * 1000` (microseconds) |
| FreeBSD | `ffTimeSleep(ddcciSleep)` between the two `ioctl()` calls |
| Windows | Not used — `GetPhysicalMonitors()` handles the timing |

DDC/CI is skipped entirely when `ddcciSleep` is `null`, and also when the built-in path already
produced a reading for every display the display server knows about. FreeBSD is stricter still: it
only tries DDC/CI when the backlight path found nothing at all.

Note that `-1` and `null` are the same thing here — see Pitfalls.

### `compact`

`compact: true` replaces the per-display lines with a single line of percentages separated by
spaces:

```
Brightness: 87% 100%
```

The compact branch calls `ffPercentAppendNum()` only, so a configured bar is silently dropped, and
like `Display`'s compact layouts it returns before reading `format`.

## Format string

Run `fastfetch -h brightness-format` for the authoritative list. Descriptions ending in `*` are
also available in the module `key` format string.

| Variable | Description |
|---|---|
| `{percentage}` | Brightness as a percentage, rendered per `percent` |
| `{name}` | Display name * |
| `{max}` | Maximum brightness value, in the device's native scale |
| `{min}` | Minimum brightness value, in the device's native scale |
| `{current}` | Current brightness value, in the device's native scale |
| `{percentage-bar}` | The same percentage as a bar |
| `{is-builtin}` | `true` when the reading came from the built-in path |

In the default layout the percentage is printed as `87%`, or as `(87%)` when a bar is shown
alongside it. In a `format` string `{percentage}` is never parenthesised.

## JSON output

```jsonc
{
    "type": "Brightness",
    "result": [
        {
            "name": "Color LCD",
            "max": 1.0,
            "min": 0.0,
            "current": 0.8749999403953552,
            "builtin": true
        },
        {
            "name": "Sample Monitor",
            "max": 100.0,
            "min": 0.0,
            "current": 100.0,
            "builtin": false
        }
    ]
}
```

The same two displays, one reporting on a `0..1` scale and one on a `0..100` scale, side by side.
The percentage is not in the JSON — a consumer has to compute it, exactly as the module does.

`name` is always present but may be empty: a display whose name could not be determined still
produces an entry. The JSON path does not check for an empty result list, so an empty `result`
array is possible where the console output would print `No result is detected.`

## Examples

```jsonc
// Number and bar together
{ "type": "brightness", "percent": { "type": [ "bar", "num" ] } }
```

```jsonc
// One line, and skip DDC/CI so nothing talks to the monitor over I2C
{ "type": "brightness", "compact": true, "ddcciSleep": null }
```

```jsonc
// Raw device values, plus the derived percentage
{ "type": "brightness", "format": "{name}: {current}/{max} = {percentage}" }
```

```jsonc
// Warn when a screen is darker than 20%
{ "type": "brightness", "percent": { "green": 20, "yellow": 80 } }
```

## Pitfalls

- **`ddcciSleep: -1` means "skip DDC/CI", the same as `null`.** `brightness.c:116-123` maps `null`
  to the sentinel `FF_BRIGHTNESS_DDCCI_SLEEP_SKIP`, which is `(uint32_t) -1`, while every other
  value goes through `(uint32_t) yyjson_get_uint(val)` — and for the integer `-1` that cast
  produces `UINT32_MAX`, the same sentinel. So `-1`, `4294967295` and `null` are three spellings of
  one behaviour, and the schema's `minimum: 0` is not enforced at runtime.
- **`{max}` and `{current}` are not comparable between displays.** The native scale is whatever the
  device uses: `0..1` on macOS built-in panels and on Haiku, `0..100` on Windows WMI and NetBSD,
  `0..max_brightness` from sysfs on Linux (commonly 255, 937 or 1200), and the monitor's own value
  from DDC/CI. Use `{percentage}` to compare, and `{current}`/`{max}` only for display.
- **`compact` silently drops the bar and the format string.** `brightness.c:25-40` returns early,
  so `"compact": true` with `"percent": { "type": [ "bar", "num" ] }` prints `Brightness: 87% 100%`
  and `"compact": true` with `"format": "FMT:{percentage}"` prints `Brightness: 87% 100%` — no
  `FMT:`, no bar, no warning.
- **`[Built-in]` / `[External]` describes the code path, not the panel.** Every implementation sets
  `builtin` from the branch that produced the reading: the platform backlight API sets it to
  `true`, the DDC/CI path sets it to `false`. An external monitor driven by a platform API, or a
  built-in panel reached over DDC/CI, is labelled the other way round.
- **A failed detection is reported as "no result", never as a reason.** Every real implementation
  of `ffDetectBrightness()` ends in `return nullptr` and discards the error strings its helpers
  produce — `"No DDC/CI compatible displays found"`, `"Permission denied when opening tty device"`,
  `"WmiOpenBlock() failed"`, `"IOAVService is not available"` are all written and then dropped
  (`brightness_linux.c:161-174`, `brightness_apple.c:234-244`, `brightness_windows.c:211-227`,
  `brightness_bsd.c:140-147`). The user sees `No result is detected.` from the module layer
  (`brightness.c:18-21`) regardless of the cause. Only `brightness_nosupport.c` returns a real
  error. On Windows, `--debug` surfaces the WMI and DDC/CI steps because that implementation is
  written with `FF_DEBUG()` calls throughout.
- **DDC/CI is the only way to read an external monitor, and it is fragile by design.** It runs over
  the monitor's I2C bus, needs a sleep between the write and the read, fails on some cables
  (HDMI adapters are a known case on macOS) and can be refused outright by the monitor. When an
  external display is missing from the output, `ddcciSleep` is worth raising before concluding that
  the display is unsupported.
- **The percentage has no guard against `max == min`.** `(current - min) / (max - min) * 100` is
  computed unconditionally (`brightness.c:33`, `:58`). No shipped backend produces `max == min`, so
  this is a code-shape observation rather than a reachable defect, but a platform that did would
  yield `nan`. `ffPercentAppendNum()` has an explicit branch for `-DBL_MAX` and none for `NaN`.
- **`percent.type` as a bare string is a hard error, not a warning.** `"type": "bar"` aborts with
  `Error: usage: percent.type must be a number or an array of strings` and exit status 224, before
  any module output.
- **`percent.green` and `percent.yellow` are limited to 0–100.** The parser rejects anything
  outside, so the "inverted" form (`green > yellow`) is expressed as two values inside that range,
  not as a sentinel.
- **Brightness re-reads the hardware on every `--dynamic-interval` round.** The `displayserver`
  cache entry is dropped at each round boundary, and this module does not use the cache layer for
  its own readings, so every round issues fresh sysfs reads and DDC/CI transactions. With a slow
  or uncooperative monitor and a short interval, that is a visible amount of I2C traffic.

## Implementation

### The shared contract

`ffDetectBrightness()` (`src/detection/brightness/brightness.h:26`) fills an `FFlist` of
`FFBrightnessResult`:

```c
typedef struct FFBrightnessResult {
    FFstrbuf name;
    double min, max, current;
    bool builtin;
} FFBrightnessResult;
```

The return value is `const char*` — `nullptr` for success. The module then computes the percentage
itself, in both output paths (`brightness.c:33` for compact, `:58` for the normal layout), and
prints `No result is detected.` when the list came back empty.

The DDC/CI constants live in the same header and are shared by every platform implementation:
the DDC/CI address `0x37` (`FF_DDC_CI_ADDR`), the EDID address `0x50`, the VCP command register
`0x51`, the "get VCP feature" opcode `0x01`, the luminance feature code `0x10`
(`FF_DDC_CI_LUMINANCE_OPCODE`), and the XOR-checksummed packet header macro
`FF_DDC_CI_MAKE_HEADER(len)`. The reply is validated by checking that the first two payload bytes
are `0x02, 0x00`, then the maximum is read from bytes 6–7 and the current value from bytes 8–9.

### Linux

Two independent paths (`brightness_linux.c`):

1. **sysfs.** `detectWithBacklight()` opens `/sys/class/backlight/` and, for each entry, reads
   `brightness` and `max_brightness`, then `realpath()`s the entry's `device` link to get the
   name. `min` is 0, `max` is whatever the kernel driver reports, and `builtin` is `true`.
2. **DDC/CI via `libddcutil`.** Compiled in only with `FF_HAVE_DDCUTIL`. The library is loaded
   with `dlopen` and the symbols are resolved at runtime; the version is handled explicitly —
   `ddca_init()` has a different signature before and after ddcutil 2.0. The sleep multiplier is
   set from `ddcciSleep / 40.0` before enumerating displays, and each display is queried with
   `ddca_get_any_vcp_value_using_explicit_type(handle, 0x10, DDCA_NON_TABLE_VCP_VALUE, …)`.
   `ddca_init()` is called with `DDCA_INIT_OPTIONS_DISABLE_CONFIG_FILE` so a user's
   `ddcutil` configuration does not change fastfetch's behaviour.

DDC/CI runs only when `ddcciSleep != SKIP` **and** the backlight path found fewer displays than the
display server reported (`:164-171`).

### FreeBSD

`detectWithBacklight()` opens `/dev/backlight/backlight0` … `backlight9` and issues
`BACKLIGHTGETSTATUS` (for the level) and `BACKLIGHTGETINFO` (for the device name, falling back to
the device node name). `max` is `BACKLIGHTMAXLEVELS`, `min` is 0. If the `backlight` kernel module
is not loaded and the first open fails with something other than `ENOENT`, the helper returns a
diagnostic that the caller discards. The `#else` branch — no `<sys/backlight.h>` — reports
`"Backlight is supported only on FreeBSD 13 and newer"`, also discarded.

DDC/CI opens `/dev/iic0` … `/dev/iic9` and sends the two-message `I2CRDWR` transaction with
`ffTimeSleep(ddcciSleep)` in between, using the same header/checksum construction as the other
platforms. The source carries a `FIXME: doesn't work for me` on that function, and its name is the
device node (`iic0`, `iic1`, …) because no EDID is read. DDC/CI here is tried only when the
backlight path returned nothing at all.

### NetBSD

`brightness_nbsd.c` reads the ACPI output brightness through sysctl, walking
`hw.acpi.acpiout0.brightness` through `hw.acpi.acpiout9.brightness` and skipping any key that
returns `-1`. Names are `acpioutN`, `max` is hardcoded to 100, `min` to 0, and every entry is
marked built-in. This reports the ACPI backlight level, which is not necessarily the same number
the desktop shows.

### OpenBSD

`brightness_obsd.c` opens `/dev/ttyC0` … `/dev/ttyC9` and issues
`ioctl(fd, WSDISPLAYIO_GETPARAM, &param)` with `param.param = WSDISPLAYIO_PARAM_BRIGHTNESS`.
`min`, `max` and `current` come from the kernel's `wsdisplay_param`. The device nodes need
permission: an `EACCES` on the very first node returns
`"Permission denied when opening tty device"` — which the caller, again, discards — and the loop
stops at the first `ENOENT`.

### Haiku

`brightness_haiku.cpp` constructs a `BApplication` (the app_server queries need a valid `be_app`),
then walks `BScreen` objects with `SetToNext()`. `GetBrightness()` returns a float on a `0..1`
scale, so `min` is 0 and `max` is 1. The name comes from `GetMonitorInfo()`, an experimental API;
when it fails the name degrades to `Screen <id>`. Everything is marked built-in.

### macOS

Two paths, both in `brightness_apple.c`:

- **Built-in.** `DisplayServicesGetBrightness()`, a weak-linked symbol from the private
  DisplayServices framework. It is queried for every display whose type is `Builtin` or `Unknown`,
  and returns a float on a `0..1` scale. If the symbol is absent at runtime, the helper returns
  `"DisplayServices function DisplayServicesGetBrightness is not available"` and the built-in
  panel is simply missing from the output.
- **External, DDC/CI.** Two implementations selected at compile time:
  - *Apple Silicon* (`__aarch64__`): `IOAVServiceCreateWithService()` on each
    `DCPAVServiceProxy` registry entry, skipping entries that declare
    `IOAVServiceUserInterfaceSupported = false` and entries whose `Location` is `Embedded` (those
    belong to the built-in path). The I2C write/read pair is `IOAVServiceWriteI2C()` /
    `IOAVServiceReadI2C()` on address `0x37`, with `ffTimeSleep(ddcciSleep)` between them. The
    name is read from EDID over address `0x50`.
  - *Intel*: `CGSServiceForDisplayNumber()` to get the framebuffer, then
    `IOFBGetI2CInterfaceCount()` / `IOFBCopyI2CInterfaceForBus()` / `IOI2CInterfaceOpen()` and a
    single `IOI2CSendRequest()` with `minReplyDelay = ddcciSleep * 1000`. The transaction type is
    negotiated first by reading `kIOI2CTransactionTypesKey` from `IOFramebufferI2CInterface`,
    preferring `kIOI2CDDCciReplyTransactionType` and falling back to `kIOI2CSimpleTransactionType`.
    The name is taken from the display server entry.

DDC/CI runs only when `ddcciSleep != SKIP` and fewer results exist than displays.

### Windows

- **Built-in.** `detectWithWmi()` opens the `WmiMonitorBrightness` WMI block with
  `WmiOpenBlock()`, queries it twice with `WmiQueryAllDataW()` (once for the size, once for the
  data), and walks the `WNODE_ALL_DATA` instances. The current value is the first byte of each
  instance's data block; `max` is hardcoded to 100, `min` to 0, `builtin` to `true`. The name is
  the middle segment of the WMI instance name — everything after the first `\` and before the
  next one. Each instance is bounds-checked against the buffer before it is read, and an invalid
  instance is skipped rather than aborting the walk.
- **External, DDC/CI.** `gdi32!GetPhysicalMonitors()` and `gdi32!GetPhysicalMonitorDescription()`
  are resolved dynamically (they are not in the import library), `GetMonitorInfoW()` maps the
  display server's `HMONITOR` to a device name, and `DestroyPhysicalMonitorInternal()` releases
  each handle. Built-in displays are skipped explicitly, since the WMI path already covered them.

WMI runs only when the display server reports a built-in or unknown display; DDC/CI runs when
`ddcciSleep != SKIP` and the result list is shorter than the display list. This implementation is
the only one that logs its progress: every step and failure goes through `FF_DEBUG()`, so
`fastfetch --debug -s brightness` explains what happened.
