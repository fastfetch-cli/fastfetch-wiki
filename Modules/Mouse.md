# Mouse

> List connected mice

| | |
|---|---|
| Module type | `mouse` |
| Default order | 64 (only used by `--gen-config`) |
| Module source | `src/modules/mouse/mouse.c` |
| Detection source | `src/detection/mouse/` |

Prints one line per pointing device, the line being just the device name. With more than one device
the key is numbered; a single device prints the bare key.

```
Mouse 1: Wireless-Receiver
Mouse 2: HID-compliant mouse
```

The serial number is read as well but is not part of the default line — it is only reachable through a
custom `format`. Devices matched by `ignores` are removed before the lines are printed.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `mouse_linux.c` | Reads `/sys/class/input/mouse*/device/name` (and `device/uniq`) |
| FreeBSD / MidnightBSD / DragonFly | `mouse_bsd.c` | USB HID report descriptors on `/dev/uhid*` |
| macOS | `mouse_apple.c` | `IOHIDManager` matched on Generic Desktop / Mouse |
| Windows | `mouse_windows.c` | Raw Input device list, then HID strings |
| Haiku | `mouse_haiku.cpp` | `get_input_devices()`, `B_POINTING_DEVICE` only |
| Android | `mouse_nosupport.c` | Reports `No mouse support on this platform` |
| NetBSD | `mouse_nosupport.c` | Not supported |
| OpenBSD | `mouse_nosupport.c` | Not supported |
| Solaris / illumos | `mouse_nosupport.c` | Not supported |
| GNU/Hurd | `mouse_nosupport.c` | Not supported |

Only Linux, the FreeBSD family, macOS, Windows and Haiku have an implementation; the other five
compile `mouse_nosupport.c`.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `ignores` | array of strings | `[]` | Drops every device whose name starts with one of these entries |
| `key` | string | `Mouse` | Module key. A single space hides the key and the separator |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

`ignores` entries are matched as a **case-insensitive prefix** against the device name, not as a
substring and not as a pattern: `"HID"` drops `HID-compliant mouse` and `hid Touchpad` alike, while
`USB HID Mouse` survives. When every device is dropped the module reports `All devices are ignored`
and prints nothing else.

## Format string

Run `fastfetch -h mouse-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{name}` | Mouse name |
| `{serial}` | Mouse serial number; empty when the device does not report one |

Neither variable is marked `*` in the help output, i.e. neither is available in the key format. A
`key` such as `M[{name}]` prints `{name}` literally; only `{index}`, `{icon}` and `{module-name}`
resolve there.

## JSON output

```jsonc
[
    {
        "type": "Mouse",
        "result": [
            { "serial": "",           "name": "Wireless-Receiver",   "ignored": false },
            { "serial": "0123456789", "name": "HID-compliant mouse", "ignored": false }
        ]
    }
]
```

- `result` is always an array. With no mouse attached it is `[]` and there is no `error` field, even
  though the text run would have reported `No devices detected`.
- `ignored` is a flag only: the JSON keeps **every** device and marks the ones `ignores` matched,
  while the text path removes them. The array can therefore be longer than the list of printed lines.
- `serial` is `""` when the device does not report one — never `null` and never omitted.
- The key order inside each object is `serial`, then `name`, then `ignored`.

## Examples

```jsonc
// Serial number next to the name
{ "type": "mouse", "format": "{name} [{serial}]" }
```

```jsonc
// Drop the virtual pointing devices this machine exposes
{ "type": "mouse", "ignores": ["HID", "Virtual", "Wireless"] }
```

```jsonc
// Keep the numbering, print only the serial
{ "type": "mouse", "format": "{serial}" }
```

## Pitfalls

- **The JSON ignores `ignores`.** It carries an `ignored` flag per device instead of dropping them, so
  a JSON consumer that wants the text output's device list has to filter on that flag itself. This is
  the same contract as [Keyboard](Modules/Keyboard).
- **Windows lists a mouse once per HID interface.** `GetRawInputDeviceList()` returns one entry per
  top-level collection and every `RIM_TYPEMOUSE` entry becomes a device, so a single mouse with
  several collections appears two or more times with the same name and serial. Nothing de-duplicates
  the list.
- **Windows can end up reporting a synthesised name.** When neither `HidD_GetProductString()` nor the
  SetupAPI device-node name (`DEVPKEY_NAME`) yields a string, the name becomes
  `Unknown device VVVV-PPPP` with the vendor and product ids in hex.
- **The Linux backend does not filter at all.** Anything that appears as `/sys/class/input/mouseN`
  with a readable `device/name` is listed: touchpads and touchscreens that expose a mouse node,
  virtual devices created by remote-desktop and gaming software, and the receiver dongle of a wireless
  mouse. There is no `is it really a mouse` check and no `IFF_RUNNING` check either, unlike the
  keyboard backend.
- **Linux serial numbers are usually empty.** The serial comes from `device/uniq`, which most mice
  leave empty; only devices that set a unique id (some Bluetooth mice) show one.
- **Haiku always reports an empty serial.** `mouse_haiku.cpp` never fills `serial`, and a device is
  only kept when its type is `B_POINTING_DEVICE` **and** `IsRunning()` is true.
- **The FreeBSD family backend adds one entry per matching HID item.** The report descriptor is walked
  with `hid_get_item()` and every item on usage page 1 / usage 2 appends a device, so a descriptor
  that declares the mouse usage more than once produces the same device several times.
- **The FreeBSD family backend stops at the first gap.** `/dev/uhid0` … `/dev/uhid63` are opened in
  order and the first `ENOENT` ends the scan, so a machine whose devices start at `uhid3` reports
  nothing.
- **Unsupported platforms report the message instead of an empty list.** `mouse_nosupport.c` returns
  `No mouse support on this platform`, which becomes an `error` in the JSON. Because
  `display.showErrors` defaults to `false`, the text run then prints nothing at all.

## Implementation

`ffDetectMouse()` fills a list of `FFMouseDevice` (two `FFstrbuf`s, `name` and `serial`) and returns
an error string. `ffPrintMouse()` and `ffGenerateMouseJsonResult()` each call it themselves, so a
`--dynamic-interval` run probes the hardware again on every round; nothing is cached.

`ffPrintMouse()` builds a second list holding pointers to the devices that survive `ignores`, numbers
the printed lines only when more than one device is left, and then releases the strings of every
device — the printed ones and the ignored ones alike. That last part is the same shape the
`keyboard` module has.

### Linux

`/sys/class/input/` is scanned for entries whose name starts with `mouse` followed by a digit, and
`device/name` under each is read into the device name, trimmed of trailing whitespace. `device/uniq`
is read into the serial and left empty when the file is missing. There is no further validation, so
any entry that exposes a name is reported.

### Windows

`GetRawInputDeviceList()` is called twice — once for the count, once for the list — and only
`RIM_TYPEMOUSE` entries are kept; `RIDI_DEVICEINFO` is used to obtain the vendor and product ids for
the fallback name. The device interface path from `RIDI_DEVICENAME` is opened with
`CreateFileW(name, 0, FILE_SHARE_READ, …)` — access 0 rather than `GENERIC_READ`, because the input
stack already owns the device — and `HidD_GetProductString()` / `HidD_GetSerialNumberString()` supply
the name and serial. When the product string is empty the interface's instance id
(`DEVPKEY_Device_InstanceId`) is resolved to a device node whose `DEVPKEY_NAME` is used instead, and
failing that the name is synthesised as `Unknown device VVVV-PPPP`. A device whose interface path
cannot be opened keeps both strings empty.

### macOS

An `IOHIDManager` is opened and matched on `kHIDPage_GenericDesktop` / `kHIDUsage_GD_Mouse`. Every
device of the resulting set contributes `kIOHIDProductKey` to the name and `kIOHIDSerialNumberKey` to
the serial; either property may be absent, leaving the corresponding string empty.

### FreeBSD / MidnightBSD / DragonFly

`/dev/uhid0` … `/dev/uhid63` are opened in order and an `ENOENT` ends the scan. Each device's report
descriptor is parsed with libusbhid (`hid_get_report_desc()` / `hid_start_parse()` / `hid_get_item()`)
and an item on usage page 1 with usage 2 marks a mouse; `ioctl(USB_GET_DEVICEINFO)` then supplies
`udi_product` and `udi_serial`.

### Haiku

`get_input_devices()` returns the system's input devices as a `BList` of `BInputDevice`. Only entries
whose `Type()` is `B_POINTING_DEVICE` and which report `IsRunning()` are kept. The name is
`BInputDevice::Name()` and the serial is always empty.
