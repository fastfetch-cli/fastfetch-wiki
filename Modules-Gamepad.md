# Gamepad

> List connected gamepads

| | |
|---|---|
| Module type | `gamepad` |
| Default order | 63 (only used by `--gen-config`) |
| Module source | `src/modules/gamepad/gamepad.c` |
| Detection source | `src/detection/gamepad/` |

Prints one line per connected controller. The value is the device name, optionally preceded by a
battery bar and optionally followed by a battery percentage in parentheses:

```
Gamepad: Sony DualSense (100%)
```

The bar and the number are both governed by `percent.type`; with the default (the global
`display.percent.type`, i.e. `num` plus `num-color`) the line is exactly the name, a space and
`(N%)`. Without `hide-others` the name is always printed, so turning the number off does not leave an
empty line. The battery number is added only for a device whose battery is in `(0, 100]` — `0` is
the module's "unknown" sentinel, and a device that reports nothing shows its name alone:

```
Gamepad: 8BitDo Pro 2
```

Two or more devices get a numbered key (`Gamepad 1`, `Gamepad 2`); a single device is printed
without a number. With no controller attached there is no line at all unless
`display.showErrors` is on, in which case the message is `No devices detected`.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `gamepad_linux.c` | `/sys/class/input/js*` |
| Android | `gamepad_nosupport.c` | Not supported |
| FreeBSD | `gamepad_bsd.c` | `/dev/uhid0` … `/dev/uhid63` |
| NetBSD | `gamepad_nosupport.c` | Not supported |
| OpenBSD | `gamepad_nosupport.c` | Not supported |
| Solaris / illumos | `gamepad_nosupport.c` | Not supported |
| Haiku | `gamepad_haiku.cpp` | `BJoystick` |
| GNU/Hurd | `gamepad_nosupport.c` | Not supported |
| macOS | `gamepad_apple.c` | IOHIDManager |
| Windows | `gamepad_windows.c` | Raw Input plus `hidsdi.h` |

The five stub platforms keep the module registered and report the standard
`Not supported on this platform` message, which is silent unless `display.showErrors` is on.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `ignores` | array of strings | – | Case-insensitive device-name **prefixes** to leave out of the text output |
| `percent.green` | integer 0–100 | `50` | Green threshold |
| `percent.yellow` | integer 0–100 | `20` | Yellow threshold |
| `percent.type` | number or array of flags | `0` (use `display.percent.type`) | `num`, `bar`, `hide-others`, `num-color`, `bar-monochrome` |
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

Two notes on the thresholds. The module's default is `green: 50, yellow: 20` — **green greater than
yellow**, which inverts the direction the schema text describes: with `green > yellow` a *high*
battery is green, a battery in `[20, 50)` is yellow and anything below `20` is red. The schema's
"values below this threshold are shown in green" only holds for the other ordering
(`green <= yellow`), which is what the battery-style modules use. And because the default
`percent.type` is `0`, the module follows the global `display.percent.type` unless you set its own —
so `--percent-type` and `display.percent.type` change this module too.

`ignores` is applied with `ffStrbufStartsWithIgnCase()`, so an entry hides every device whose name
*starts* with it: `"Sony"` hides all Sony controllers, and an empty string would hide everything.

## Format string

Run `fastfetch -h gamepad-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{name}` | Device name |
| `{serial}` | Serial number, empty when the backend has none |
| `{battery-percentage}` | The number, e.g. `100%`, empty unless `percent.type` includes `num` |
| `{battery-percentage-bar}` | The bar, empty unless `percent.type` includes `bar` |

None of the four is available in the key format: `ffPrintLogoAndKey()` parses `key` with `{index}`,
`{icon}` and `{module-name}` only. `{index}` **is** available in the output format and follows the
printed line number — `0` for a single controller, `1` for the first of several.

Two details of the shared format engine are easy to get wrong:

- **An unknown placeholder is copied verbatim, conditional syntax included.** A key of
  `"[{?index}I{index}{?}][{?name}N{name}{?}]"` prints `[][{?name}N{name}{?}]`.
- **Conditional blocks treat `0` and `""` as unset.** `formatArgSet()` counts an integer as set only
  when it is `> 0` and a string only when its length is non-zero. `{?index}…{?}` therefore stays
  silent for a single controller, and `{?serial}…{?}` stays silent for a device without a serial.

## JSON output

```jsonc
[
    {
        "type": "Gamepad",
        "result": [
            {
                "serial": "",
                "name": "Sony DualSense",
                "battery": 0,
                "ignored": false
            }
        ]
    }
]
```

The array is always present — `"result": []` when nothing is connected, with no `error` field even
though the print path would have said `No devices detected`. `ignored` is only a flag; the JSON path
**does not remove** ignored devices, unlike the text path.

`battery` is the raw value of `FFGamepadDevice.battery`, so it carries the same `0`-means-unknown
convention as the text path: a DualSense in that sample reports no battery at all rather than 0%.
JSON is the only place where the `ignored` flag and the battery of an ignored device are visible —
the text path drops ignored devices before printing, so their battery never reaches the terminal.

## Examples

```jsonc
// Hide everything whose name starts with "Xbox" or "8BitDo"
{ "type": "gamepad", "ignores": ["Xbox", "8BitDo"] }
```

```jsonc
// Battery bar instead of the name, number included
{ "type": "gamepad", "percent": { "type": ["bar", "num", "hide-others"] }, "key": "PAD {index}" }
```

```jsonc
// Name only: the number is suppressed by dropping the "num" flag
{ "type": "gamepad", "percent": { "type": ["bar-monochrome"] } }
```

Each of these is accepted by the parser; the line they produce depends on a controller being
attached, and on the model, because only some models report a battery (see the Pitfalls below).

## Pitfalls

- **The JSON list and the text list can have different lengths.** The text path filters `ignores`
  out, the JSON path keeps every device and marks it with `ignored: true`. A config that hides a
  controller therefore prints fewer lines than the JSON array has objects, and index-based
  comparisons between the two drift apart.
- **`{battery-percentage-bar}` is empty by default.** The bar string is only built when
  `percent.type` includes the `bar` flag, and the default type does not. A format like
  `{battery-percentage-bar} {name}` prints a leading space and no bar unless you add the flag,
  whereas the default line is unaffected because it appends the bar inline rather than through the
  variable.
- **A battery of `0` is hidden, not printed as `0%`.** `0` means "unknown" in this module, so a
  controller that is genuinely empty and one the backend could not read look identical: both print
  the bare name. Negating the value is not possible from a format string.
- **On Windows only three controller models ever report a battery.** The battery read is guarded by
  a vendor/product check — Sony `0x054C`/`0x05C4` and `0x09CC` (DualShock 4), and Nintendo
  `0x057E`/`0x2009` (Switch Pro). Every other controller, Xbox pads included, keeps `battery = 0`
  and never shows a percentage.
- **On Windows the battery only appears after another program has opened the controller.**
  Immediately after pairing, fastfetch usually reports no battery even for a DualShock 4 or a Switch
  Pro; starting a program that actually uses the pad (Steam, DS4Windows, a browser gamepad tester)
  and re-running fastfetch then shows it. The source comment at that read is
  `// Controller must be connected by other programs`, and the read is a single
  `ReadFile()` on an overlapped handle that is waited for at most one second
  (`FF_IO_TERM_RESP_WAIT_MS`, a constant that otherwise belongs to terminal-response handling). The
  module does no handshake of its own, so a controller that stays silent until a client unlocks it
  yields nothing. (Tracked as an open item in the project's `bug.md`.)
- **On Windows a fully drained Switch Pro reports 1%.** The Switch path maps a battery level of `0`
  to `1` on purpose, because `0` would otherwise mean "unknown" and the number would disappear.
- **On macOS the battery is always unknown.** `gamepad_apple.c` never touches the field, so macOS
  lines are always name-only.
- **On macOS the device order is not stable.** The devices come from a `CFSet`
  (`IOHIDManagerCopyDevices()` and `CFSetApplyFunction()`), which has no defined order, so with two
  controllers attached the lines — and the `Gamepad 1` / `Gamepad 2` numbers — can swap between
  runs. Linux and Windows enumerate in a stable OS order.
- **On FreeBSD the same controller can be listed twice.** The report descriptor is walked and an
  entry is appended for *every* matching top-level usage (page 1, usage 1, 4 or 5) without a
  duplicate check or a `break`, so a device that advertises two of those usages produces two
  identical lines with two different index numbers.
- **On Linux the battery probe stops at the first directory that answers.** The scan of
  `.../power_supply/*` reads `capacity` and, failing that, `<name>_level`, and `break`s as soon as
  one of them returns data. A device whose `power_supply` directory holds more than one entry
  therefore reports whichever entry the kernel happened to list first.
- **On Linux the Switch Pro level is a five-step ladder, not a percentage.** `_level` is a word —
  `Critical` → 1, `Low` → 25, `Normal` → 50, `High` → 75, `Full` → 100 — so the bar can only ever
  show five values on that controller.
- **Nothing at all is printed when `ignores` matches every device**, unless
  `display.showErrors` is on, where the message is `All devices are ignored`. The JSON path still
  lists the devices.
- **`ffPrintError()` writes to stdout.** With `display.showErrors: true` an error line is printed
  before the JSON array, so the run is no longer parseable. Keep `showErrors` off in anything that
  pipes the JSON output.

## Implementation

`ffPrintGamepad()` and `ffGenerateGamepadJsonResult()` both call `ffDetectGamepad()` on a fresh list
of `FFGamepadDevice`, but they treat `ignores` differently: the print path builds a second list of
pointers to the devices that survive the filter and prints from that, while the JSON path iterates
the full list and writes the `ignored` flag. Neither path is cached, so a `--dynamic-interval` run
re-enumerates the controllers every round.

### Windows

`GetRawInputDeviceList()` supplies the HID devices; each one must have `usUsagePage == 1` and
`usUsage` 4 (Joystick) or 5 (Gamepad). The name comes from a small built-in table keyed by
vendor/product id — Nintendo `0x057E`, Sony `0x054C`, Logitech `0x046D`; Microsoft `0x045E` is
deliberately left to Windows' own naming. When the device cannot be opened at all and is not in the
table, the name becomes `Unknown gamepad VVVV-PPPP`. For other devices
`HidD_GetProductString()` and `HidD_GetManufacturerString()` are combined into
`Manufacturer Product`, and the serial comes from `HidD_GetSerialNumberString()`.

The battery is read only for the DualShock 4 and Switch Pro ids listed above, from a single input
report obtained through an overlapped `ReadFile()` with a one-second wait. The DualShock 4 path reads
byte 30 or 32 depending on whether the report length is 64, masks a 4-bit level and divides by 11
while charging or 8 otherwise; the Switch path requires report id `0x30` and takes three bits from
byte 2. Both clamp the result to 100.

### Linux

`/sys/class/input/` is scanned for names starting with `js` followed by a digit. The device name is
the contents of `.../device/name`, and the serial is `uniq`. The battery comes from
`.../device/device/power_supply/<entry>/capacity` when that file has data, otherwise from
`<entry>_level`, whose word is mapped through the five-step ladder described above.

### FreeBSD, DragonFly

`/dev/uhidN` is opened for N = 0…63, with `ENOENT` ending the loop and any other error continuing to
the next index. `hid_get_report_desc()` / `hid_start_parse()` walk the report descriptor looking for a
top-level usage of page 1 with usage 1, 4 or 5 (usage 1 is accepted because FreeBSD reports a Pro
Controller that way), and `USB_GET_DEVICEINFO` supplies `udi_vendor` + `udi_product` as the name and
`udi_serial` as the serial. No battery is read.

### macOS

`IOHIDManager` is created and matched against `GenericDesktop` / `Joystick` and `GenericDesktop` /
`GamePad`, then open for the lifetime of the call. The name is
`kIOHIDManufacturerKey` + ` ` + `kIOHIDProductKey` when a manufacturer is present, otherwise the
product alone; the serial is `kIOHIDSerialNumberKey`. No battery is read.

### Haiku

`BJoystick::CountDevices()` / `GetDeviceName()` supply the names; serial and battery stay empty.
