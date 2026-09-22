# Bluetooth

> List connected Bluetooth devices

| | |
|---|---|
| Module type | `bluetooth` |
| Default order | 59 (only used by `--gen-config`) |
| Module source | `src/modules/bluetooth/bluetooth.c` |
| Detection source | `src/detection/bluetooth/` |

Prints one line per Bluetooth device the platform reports: the device name, its battery level when
the platform knows one, and a `[disconnected]` marker for a device that is paired but not currently
connected.

```
Bluetooth 1: Wireless Headset (80%)
Bluetooth 2: Bluetooth Mouse [disconnected]
```

The key is the module name followed by a 1-based device number when more than one device is
printed, and the bare module name for a single device; a custom `key` replaces it.

The battery level goes through the shared percentage helpers, so it follows `display.percentType`
and this module's own `percent` object. With the default style the number is appended in
parentheses after the name; switching the style to `"bar"` prints a bar *before* the name instead,
and `"hide-others"` drops the name so that only the percentage is left.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `bluetooth_linux.c` | BlueZ over the system D-Bus (`GetManagedObjects`); covers classic **and** single-mode LE devices |
| Android | `bluetooth_nosupport.c` | Reports "Not supported on this platform" |
| GNU/Hurd | `bluetooth_linux.c` | Same file as Linux |
| FreeBSD / MidnightBSD | `bluetooth_bsd.c` | Netgraph `bt_devenum()`; names come from `bt_devremote_name_gen()` |
| DragonFly | `bluetooth_nosupport.c` | The FreeBSD block's `if(DragonFly)` branch |
| NetBSD | `bluetooth_bsd.c` | Same file, but there is no `bt_devremote_name_gen()`, so the name is `dev->devname` |
| OpenBSD | `bluetooth_nosupport.c` | Not supported |
| Solaris / illumos | `bluetooth_nosupport.c` | Not supported |
| Haiku | `bluetooth_haiku.cpp` | Reports the local adapter itself, not remote devices |
| macOS | `bluetooth_apple.m` | `IOBluetoothDevice.pairedDevices` — classic devices only |
| Windows | `bluetooth_windows.c` | `bluetoothapis` device search, plus a `cfgmgr32` battery lookup — classic devices only |

**Only the Linux backend can enumerate Bluetooth Low Energy devices.** Both the Windows and the
macOS backend are built on the classic (BR/EDR) stacks, and fastfetch contains no BLE code path at
all. On Linux, LE devices reach the list because BlueZ exposes them through the same
`org.bluez.Device1` interface as classic devices.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `showDisconnected` | boolean | `false` | Also list devices that are remembered or paired but not connected |
| `percent` | object | `{ "green": 50, "yellow": 20, "type": 0 }` | Colour thresholds and style for the battery percentage. `type: 0` inherits `display.percentType`. |
| `key` | string | `Bluetooth <n>` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

The default `percent` uses `green: 50 > yellow: 20`, the *inverted* interpretation: 50–100 % is
green, 20–50 % is yellow and 0–20 % is red. The two thresholds are explained in
[Configuration](Configuration#displaypercenttype).

## Format string

Run `fastfetch -h bluetooth-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{name}` | Device name |
| `{address}` | Device address |
| `{type}` | Device type, as far as the platform can tell |
| `{battery-percentage}` | Battery level as a number; `0%` when no level is known |
| `{connected}` | `true` or `false` |
| `{battery-percentage-bar}` | Battery level as a bar |

None of these carries a `*` in `fastfetch -h bluetooth-format`, so **no device data is available in
the key format**: a custom `key` can only use `{index}`, `{icon}` and `{module-name}`. `{index}` is
the same 1-based device number the default key prints.

## JSON output

```jsonc
{
    "type": "Bluetooth",
    "result": [
        {
            "address": "AA:BB:CC:DD:EE:FF",
            "battery": 80,
            "connected": true,
            "name": "Wireless Headset",
            "type": "Audio"
        }
    ]
}
```

`battery` is `0` when the platform has no level for the device, and `connected` is always `true` on
the BSDs and Haiku, which have no way to tell. An empty `result` array is a normal outcome, not an
error. On failure the object is `{ "type": "Bluetooth", "error": "…" }`.

## Examples

```jsonc
// Show the type and battery level instead of the name
{ "type": "bluetooth", "format": "{name} [{type}] {battery-percentage}" }
```

```jsonc
// Include paired-but-offline devices, with a bar
{ "type": "bluetooth", "showDisconnected": true, "percent": { "type": ["bar", "num"] } }
```

```jsonc
// Number the lines and hide the built-in key
{ "type": "bluetooth", "key": "BT {index}" }
```

## Pitfalls

- **Nothing is printed when no device is found — not even a message.** The "No bluetooth devices
  found" text goes through `ffPrintError()`, which returns immediately unless
  `display.showErrors` is `true`. A blank line plus exit code 0 is the normal result on a machine
  with a working radio and nothing paired, and `--format json` is the only way to tell that apart
  from a module that is not even in the structure — it reports `"result": []`.
- **In a custom `format`, the battery variables are not guarded.** `{battery-percentage}` is `0%`
  and `{battery-percentage-bar}` is an empty bar for a device without a reported level, because the
  format path calls the percentage helpers unconditionally; only the default output skips them when
  the level is unknown or out of range.
- **Bluetooth Low Energy devices are only enumerated on Linux.** `bluetooth_windows.c` and
  `bluetooth_apple.m` use classic-only APIs, and Microsoft says so explicitly for the one fastfetch
  calls: "The BluetoothFindFirstDevice function does not find Bluetooth Low Energy (LE) devices."
  On Linux the BlueZ object manager returns LE devices in the same `org.bluez.Device1` list, so they
  do appear — but only once BlueZ knows about them, because fastfetch never scans.
- **`{type}` means different things on different platforms.** Windows and macOS decode the
  Bluetooth service/device class bits into a list such as `Audio, Rendering` or a single major class
  such as `Audio/Video`; Linux uses BlueZ's freedesktop `Icon` property verbatim, so it prints
  values like `audio-headset`; the BSDs leave it empty.
- **`showDisconnected` has no effect on the BSDs or on Haiku.** Those backends hard-code
  `connected = true` for every device they report, so the filter can never match.
- **On Linux a device BlueZ reports as unpaired loses its name.** The `Paired` property is used as
  an early exit that clears whatever has been collected and skips the rest of that interface's
  properties; the device is still added to the list and the printer substitutes `Unknown Device`.
- **Linux needs a D-Bus build.** Without `libdbus` the module reports
  `Fastfetch was compiled without DBus support`, and the same is true of the BlueZ build in a
  container without a system bus.
- **The module never scans.** Discovery is whatever the platform already knows: connected devices,
  plus remembered/pairing state when `showDisconnected` is on. On Windows the underlying
  `BLUETOOTH_DEVICE_SEARCH_PARAMS` sets neither `fIssueInquiry` nor `fReturnUnknown`, so there is no
  discovery at all there.

## Implementation

`ffDetectBluetooth()` fills a list of `FFBluetoothResult` (`name`, `address`, `type`, `battery`,
`connected`); `ffPrintBluetooth()` prints one line per entry with `printDevice()`, and
`ffGenerateBluetoothJsonResult()` serialises the same list. Both call the detector themselves, so a
`--format json` run detects twice if the module appears twice in the structure. Nothing is cached,
so a `--dynamic-interval` run re-probes on every round.

The battery figure is a `uint8_t`, `0` meaning "unknown", and the default printer only shows it when
`0 < battery <= 100`.

### Linux

`org.freedesktop.DBus.ObjectManager.GetManagedObjects` on `org.bluez` returns a tree of objects;
every object path containing `/dev_` is taken as a device and its `org.bluez.Device1` /
`org.bluez.Battery1` properties are read: `Address`, `Name`, `Icon` (into `type`), `Connected`,
`Paired` and `Percentage`. Adapter objects (`/org/bluez/hci0`) are skipped because their path has no
`/dev_`. A device with an empty name becomes `Unknown Device`.

When `showDisconnected` is off the module first counts connected devices by listing
`/sys/class/bluetooth` and counting the entries whose name contains `:`, returning an empty list
immediately when that count is zero; it then drops the entries that are not connected while walking
the D-Bus reply and stops early once it has seen as many connected devices as the kernel reported.

### macOS

`IOBluetoothDevice.pairedDevices` is the source. The address is converted from the framework's
`AA-BB-CC-DD-EE-FF` form to upper-case colon form, and the battery is taken from one of the private
`batteryPercent*` properties (`Single` first, then `Combined`, then `Case`). The type string is the
`serviceClassMajor` bits in the same order and wording as the Windows backend, falling back to
`deviceClassMajor` when no service bit is set.

### Windows

`BluetoothFindFirstDevice()` / `BluetoothFindNextDevice()` from `bluetoothapis.dll` are loaded
dynamically, with `fReturnConnected` always on and `fReturnRemembered` / `fReturnAuthenticated`
following `showDisconnected`. The type is decoded from `ulClassofDevice`: bits 13–23 name the
service classes (`Limited Discoverable Mode` … `Information`) and, when none of them is set, bits
8–12 give the major class (`Computer`, `Phone`, `Audio/Video`, …). The address is rendered from the
six bytes in reverse order.

Battery levels are not in that structure, so a second pass enumerates the *media* class devices with
`CM_Get_Device_ID_ListW()` and looks for `DEVPKEY_Bluetooth_BatteryLevel` on each node — handsets
and headsets tend to expose it through the Hands-Free profile's media node rather than through the
Bluetooth device node. The address property is matched byte-for-byte against the already-collected
devices (skipping the colons), and a device no property matches simply keeps `0`.

### FreeBSD, NetBSD and Haiku

The BSDs walk the Netgraph Bluetooth device list with `bt_devenum()`; FreeBSD adds
`bt_devremote_name_gen()` for the remote name, NetBSD uses `dev->devname` directly. Battery, type
and connection state are all left at their defaults, and `showDisconnected` is ignored.

Haiku asks `Bluetooth::LocalDevice::GetLocalDevice()` — the *local* adapter — and reports it as the
single device in the list, with the device class dumped by `GetDeviceClass()`. Remote devices are
not enumerated at all.
