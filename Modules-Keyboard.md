# Keyboard

> List connected keyboards

| | |
|---|---|
| Module type | `keyboard` |
| Default order | 65 (only used by `--gen-config`) |
| Module source | `src/modules/keyboard/keyboard.c` |
| Detection source | `src/detection/keyboard/` |

Prints one line per keyboard, the line being just the device name. With more than one device the key is
numbered; a single device prints the bare key.

```
Keyboard 1: HID Keyboard Device
Keyboard 2: USB Keyboard
```

The serial number is read as well, but it is not part of the default line — it is only reachable through
a custom `format`. Devices matched by `ignores` are removed before the lines are printed.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `keyboard_linux.c` | Parses `/proc/bus/input/devices` |
| FreeBSD / MidnightBSD / DragonFly | `keyboard_bsd.c` | USB HID first, console `KDGKBINFO` ioctl as fallback |
| macOS | `keyboard_apple.c` | `IOHIDManager` matched on Generic Desktop / Keyboard |
| Windows | `keyboard_windows.c` | Raw Input device list, then HID strings |
| Haiku | `keyboard_haiku.cpp` | `get_input_devices()` |
| Android | `keyboard_nosupport.c` | Reports `No keyboard support on this platform` |
| NetBSD | `keyboard_nosupport.c` | Not supported |
| OpenBSD | `keyboard_nosupport.c` | Not supported |
| Solaris / illumos | `keyboard_nosupport.c` | Not supported |
| GNU/Hurd | `keyboard_nosupport.c` | Not supported |

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `ignores` | array of strings | `[]` | Drops every device whose name starts with one of these entries |
| `key` | string | `Keyboard` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

`ignores` entries are matched as a **case-insensitive prefix** against the device name, not as a
substring and not as a pattern: `"HID"` drops `HID Keyboard Device` and `hid Touchpad Keyboard` alike,
while `USB HID Keyboard` survives. When every device is dropped the module reports
`All devices are ignored` and prints nothing else.

## Format string

Run `fastfetch -h keyboard-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{name}` | Device name |
| `{serial}` | Serial number; empty when the device does not report one |

Neither variable is available in the key format — `fastfetch -h keyboard-format` marks the ones that are
with a `*`, and only the four other modules that own their key pass device data into it. A `key` such as
`K[{name}]` prints `{name}` literally; `{index}`, `{icon}` and `{module-name}` do work there.

## JSON output

```jsonc
[
    {
        "type": "Keyboard",
        "result": [
            { "serial": "",           "name": "HID Keyboard Device", "ignored": false },
            { "serial": "0123456789", "name": "USB Keyboard",        "ignored": false }
        ]
    }
]
```

- `result` is always an array. With no keyboard attached it is `[]` and there is no `error` field, even
  though the text run would have reported `No devices detected`.
- `ignored` is a flag only: the JSON keeps **every** device and marks the ones `ignores` matched, while
  the text path removes them. The array can therefore be longer than the list of printed lines.
- `serial` is `""` when the device does not report one — never `null`, and never omitted.

## Examples

```jsonc
// The serial number next to the name
{ "type": "keyboard", "format": "{name} [{serial}]" }
```

```jsonc
// Drop the virtual keyboards this machine exposes
{ "type": "keyboard", "ignores": ["HID", "Virtual", "Wireless"] }
```

```jsonc
// Keep the key numbering, but show the serial instead of the name
{ "type": "keyboard", "format": "{serial}" }
```

## Pitfalls

- **The JSON ignores `ignores`.** It carries an `ignored` flag per device instead of dropping them, so a
  JSON consumer that wants the text output's device list has to filter on that flag itself.
- **Windows lists a keyboard once per HID interface.** `GetRawInputDeviceList()` returns one entry per
  top-level collection, and every entry becomes a device, so a keyboard with several collections appears
  two or more times with the same name and serial. Nothing de-duplicates the list.
- **Windows can end up reporting a synthesised name.** When neither `HidD_GetProductString()` nor the
  SetupAPI device-node name (`DEVPKEY_NAME`) yields a string, the name becomes
  `Unknown device VVVV-PPPP`, with the vendor and product ids in hex. This is the only place the module
  invents a name instead of reading one.
- **On FreeBSD the fallback describes the console keyboard, not a device.** When no USB HID keyboard is
  found, the `KDGKBINFO` ioctl produces a name like `AT 101/102-key keyboard (kbd0)` — or
  `Unknown keyboard (kbd0)` for a type it does not know — and the serial is always empty.
- **Linux drops a lot of plausible candidates silently.** An entry in `/proc/bus/input/devices` is only
  kept when its `Handlers` line contains `kbd`, its `EV` bitmap has `EV_REP` set and its `KEY` bitmap has
  `KEY_A` set; anything failing one of the three checks is skipped without a message. That is how power
  buttons, PC speakers, media remotes and headset buttons stay out of the list — but it also means a
  keyboard that does not advertise `KEY_A` never shows up.
- **Linux reports an empty serial for most keyboards.** The serial comes from the `U: Uniq=` line, which
  USB keyboards usually leave empty; only devices that set a unique id (some Bluetooth and gaming
  keyboards) show one.
- **Linux skips an entry with no name.** A device block is only turned into a device at the blank line
  between blocks, and only when a `N: Name=` line was seen before it.

## Implementation

`ffDetectKeyboard()` fills a list of `FFKeyboardDevice` (two `FFstrbuf`s, `name` and `serial`) and returns
an error string. `ffPrintKeyboard()` and `ffGenerateKeyboardJsonResult()` each call it themselves, so a
`--dynamic-interval` run probes the hardware again on every round; nothing is cached.

`ffPrintKeyboard()` builds a second list holding pointers to the devices that survive `ignores`, numbers
the printed lines only when more than one device is left, and then releases the strings of every device —
the printed ones and the ignored ones alike.

### Linux

`/proc/bus/input/devices` is read in full and parsed line by line, with a blank line ending each device
block. Four line types matter:

- `N: Name=` → the device name, with the surrounding quotes trimmed.
- `H: Handlers=` → a space-separated handler list that has to contain `kbd`.
- `B: EV=` → the event-type bitmap; bit 20 (`EV_REP`, auto-repeat) has to be set.
- `B: KEY=` → the key bitmap, given as space-separated hex words, most significant word first; `KEY_A`
  (bit 30) is read out of the last, least significant word.
- `U: Uniq=` → the serial number.

The device is added when the block ends, and only if it has a name. Entries that fail the handler or
bitmap checks are skipped to the end of their block.

### Windows

`GetRawInputDeviceList()` is called twice — once for the count, once for the list — and only
`RIM_TYPEKEYBOARD` entries are kept. `RIDI_DEVICENAME` gives a device *interface* path, which is opened
with `CreateFileW(name, 0, FILE_SHARE_READ, …)`; access 0 rather than `GENERIC_READ` is required because
the input stack already owns the device. `HidD_GetProductString()` and `HidD_GetSerialNumberString()`
supply the name and serial. If the product string is empty, the interface's instance id
(`DEVPKEY_Device_InstanceId`) is resolved to a device node whose `DEVPKEY_NAME` is used instead, and
failing that the name is synthesised from the vendor and product ids as `Unknown device VVVV-PPPP`.
A device whose interface path cannot be opened at all keeps both strings empty.

### FreeBSD / MidnightBSD / DragonFly

`/dev/uhid0` … `/dev/uhid63` are opened in order, and an `ENOENT` ends the scan. Each device's report
descriptor is parsed with libusbhid (`hid_start_parse()` / `hid_get_item()`); an item on usage page 1 with
usage 6 marks the device as a keyboard, and `ioctl(USB_GET_DEVICEINFO)` then supplies `udi_product` and
`udi_serial`. When the USB scan found nothing, the console keyboard is described through
`ioctl(KDGKBINFO)`: `KB_84` and `KB_101` map to fixed "AT…keyboard" names, everything else to
`Unknown keyboard`, and `kb_index` is appended as `(kbdN)`.

### macOS

An `IOHIDManager` is opened and matched on `kHIDPage_GenericDesktop` / `kHIDUsage_GD_Keyboard`. Every
device of the resulting set contributes `kIOHIDProductKey` to the name and `kIOHIDSerialNumberKey` to the
serial; either property may be absent, in which case the corresponding string stays empty.

### Haiku

`get_input_devices()` returns the system's input devices as a `BList` of `BInputDevice`. Only entries
whose `Type()` is `B_KEYBOARD_DEVICE` and which report `IsRunning()` are kept. The name is
`BInputDevice::Name()` and the serial is always empty.
