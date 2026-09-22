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
and `"hide-others"` drops the name so that only the percentage is left. The signal strength the
platform knows about, and which Bluetooth stacks the device was seen over, are reported in the
custom format and in the JSON output rather than in the default line.

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
| Windows | `bluetooth_windows.c`, `bluetooth_windows.cpp` | `bluetoothapis` for BR/EDR plus a WinRT query for Low Energy; the two lists are joined per device |

**Both stacks are enumerated on Linux and on Windows.** Windows keeps them behind two unrelated
APIs — `bluetoothapis` sees BR/EDR devices only and says so in its own documentation, while the Low
Energy half needs a WinRT `DeviceInformation` query — so the two results are joined afterwards. On
macOS and on the BSDs the backend only ever walks a classic stack, and Haiku reports the local
adapter, so on those platforms `deviceType` can only ever be `Classic`.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `showDisconnected` | boolean | `false` | Also list devices that are remembered or paired but not connected |
| `percent` | object | `{ "green": 50, "yellow": 20, "type": 0 }` | Colour thresholds and style, shared by the battery and the signal quality. `type: 0` inherits `display.percentType`. |
| `key` | string | `Bluetooth <n>` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

The default `percent` uses `green: 50 > yellow: 20`, the *inverted* interpretation: 50–100 % is
green, 20–50 % is yellow and 0–20 % is red. The two thresholds are explained in
[Configuration](Configuration#displaypercenttype). One `percent` object serves both percentages this
module prints, so changing it for the battery changes it for the signal quality as well; they are
both "higher is better, 0–100", so the thresholds carry over.

## Format string

Run `fastfetch -h bluetooth-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{name}` | Device name |
| `{address}` | Device address |
| `{type}` | Device type, as far as the platform can tell |
| `{device-type}` | Stacks the device answers on: `Classic`, `Low Energy`, or both joined with `, ` |
| `{battery-percentage}` | Battery level as a number; `0%` when no level is known |
| `{battery-percentage-bar}` | Battery level as a bar |
| `{signal-quality}` | Signal strength as a number; empty when the platform has none |
| `{signal-quality-bar}` | Signal strength as a bar; empty when the platform has none |
| `{connected}` | `true` or `false` |

`{battery-percentage}` and `{signal-quality}` both follow `display.percentType` (and this module's
`percent.type`), so by default only the numbers are produced and the two `-bar` variables are empty.
Set `"percent": { "type": ["num", "bar"] }` to get both.

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
            "deviceType": ["Classic", "Low Energy"],
            "name": "Wireless Headset",
            "signalQuality": 74.0,
            "type": "Audio"
        }
    ]
}
```

`battery` is `0` when the platform has no level for the device, and `connected` is always `true` on
the BSDs and Haiku, which have no way to tell. `signalQuality` is `null` when the platform has no
figure for it — which is every device on the BSDs, Haiku and macOS, and every classic-only device on
Windows. `deviceType` is an array holding `"Classic"`, `"Low Energy"` or both, in that order, and is
empty when the platform could not tell which stack the device came over. An empty `result` array is
a normal outcome, not an error. On failure the object is `{ "type": "Bluetooth", "error": "…" }`.

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
// Which stack each device came over, and how strong the link is
{ "type": "bluetooth", "showDisconnected": true, "format": "{name} [{device-type}] {signal-quality}" }
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
  the level is unknown or out of range. The signal-quality pair behaves the other way round: both
  variables are empty for a device whose signal strength is unknown.
- **Bluetooth Low Energy devices are enumerated on Linux and on Windows, and nowhere else.**
  `bluetooth_apple.m` uses `IOBluetoothDevice.pairedDevices`, a BR/EDR API. On Linux the BlueZ object
  manager returns LE devices in the same `org.bluez.Device1` list as classic devices. On Windows the
  LE half is a separate WinRT query, which the build only enables when the WinRT headers are
  available: a Windows build with g++ compiles it out (CMake prints `WinRT support is disabled due
  to known issues with g++`), and that build then reports `Classic` for everything and `null` for
  every signal quality.
- **On Windows, an LE device is only found once it has been paired.** The LE half asks for
  `BluetoothLEDevice::GetDeviceSelectorFromPairingState(true)`, so an LE peripheral that is in range
  but has never been paired is not in the result — not even with `showDisconnected`, which only lifts
  the connected filter. The same is true of the classic half, whose search parameters set neither
  `fIssueInquiry` nor `fReturnUnknown`.
- **On Windows, an LE-only device is listed with its LE address, and a dual-mode one with its classic
  address.** The device tree holds one node per *enumerator*, so a phone paired over both stacks has
  a `BTHENUM\Dev_*` node and a separate `BTHLE\Dev_*` node, each with its own address, sharing one
  container id. fastfetch joins the two on that container id and prints the classic address, because
  that is the one the Settings app shows; a device that only speaks LE has no classic node and keeps
  its LE address.
- **On Windows, an LE-only device never has a battery level.** The level comes from
  `DEVPKEY_Bluetooth_BatteryLevel`, which Windows only publishes on BR/EDR service nodes (on the
  machine this was verified against, exactly one node in the whole tree carried it, a `BTHENUM`
  Hands-Free one, for a dual-mode headset; none of the 61 `BTHLE` / `BTHLEDevice` nodes did). An LE
  peripheral whose vendor app shows a battery therefore still prints `0` here.
- **`{type}` means different things on different platforms.** Windows and macOS decode the
  Bluetooth service/device class bits into a list such as `Audio, Rendering` or a single major class
  such as `Audio/Video`; Linux uses BlueZ's freedesktop `Icon` property verbatim, so it prints
  values like `audio-headset`; the BSDs leave it empty. A device that only speaks LE has no class of
  device at all, so Windows falls back to the LE appearance category, which is much coarser — a pair
  of earbuds reports `Audio Sink`, not `Rendering, Audio`.
- **`signalQuality` is the LE endpoint's last known figure, not a live link measurement.** It is
  converted from `System.Devices.Aep.SignalStrength` (Windows) or the `RSSI` property (Linux) with
  the same `-50 dBm = 100 %` … `-100 dBm = 0 %` scale the `Wifi` module uses. It stays readable
  while the link is down, so it can be present on a device whose `connected` is `false`, and it says
  nothing about the classic link of a dual-mode device.
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
  discovery at all there, and the LE query is restricted to already-paired devices.

## Implementation

`ffDetectBluetooth()` fills a list of `FFBluetoothResult` (`name`, `address`, `type`, `deviceType`,
`battery`, `signalQuality`, `connected`); `ffPrintBluetooth()` prints one line per entry with
`printDevice()`, and `ffGenerateBluetoothJsonResult()` serialises the same list. Both call the
detector themselves, so a `--format json` run detects twice if the module appears twice in the
structure. Nothing is cached, so a `--dynamic-interval` run re-probes on every round.

The battery figure is a `uint8_t`, `0` meaning "unknown", and the default printer only shows it when
`0 < battery <= 100`. The signal quality is a `double` whose "unknown" is `-DBL_MAX`, which is the
same sentinel the `Wifi` module uses; `deviceType` is a bitfield with one bit per stack.

### Linux

`org.freedesktop.DBus.ObjectManager.GetManagedObjects` on `org.bluez` returns a tree of objects;
every object path containing `/dev_` is taken as a device and its `org.bluez.Device1` /
`org.bluez.Battery1` properties are read: `Address`, `Name`, `Icon` (into `type`), `Connected`,
`Paired` and `Percentage`. Adapter objects (`/org/bluez/hci0`) are skipped because their path has no
`/dev_`. A device with an empty name becomes `Unknown Device`.

Three more properties feed the two newer fields: `Class` sets the classic bit and `Appearance` the
LE bit, because the class of device is a BR/EDR concept that BlueZ only publishes for devices seen
over classic, while the appearance is a GATT characteristic that only LE devices have. `RSSI` (an
`int16` in dBm, present while the device is connected) is converted into the signal quality.

When `showDisconnected` is off the module first counts connected devices by listing
`/sys/class/bluetooth` and counting the entries whose name contains `:`, returning an empty list
immediately when that count is zero; it then drops the entries that are not connected while walking
the D-Bus reply and stops early once it has seen as many connected devices as the kernel reported.

### macOS

`IOBluetoothDevice.pairedDevices` is the source. The address is converted from the framework's
`AA-BB-CC-DD-EE-FF` form to upper-case colon form, and the battery is taken from one of the private
`batteryPercent*` properties (`Single` first, then `Combined`, then `Case`). The type string is the
`serviceClassMajor` bits in the same order and wording as the Windows backend, falling back to
`deviceClassMajor` when no service bit is set. This is a classic-only API, so every device is
`Classic` and no signal quality is available.

### Windows

The classic half lives in `bluetooth_windows.c`.
`BluetoothFindFirstDevice()` / `BluetoothFindNextDevice()` from `bluetoothapis.dll` are loaded
dynamically, with `fReturnConnected` always on and `fReturnRemembered` / `fReturnAuthenticated`
following `showDisconnected`. The name and the address come from the returned
`BLUETOOTH_DEVICE_INFO` (`szName` and the six address bytes in reverse order) rather than from the
device node's friendly name, which is empty for a device whose driver never filled it in. The type
is decoded from `ulClassofDevice`: bits 13–23 name the service classes (`Limited Discoverable Mode`
… `Information`) and, when none of them is set, bits 8–12 give the major class (`Computer`,
`Phone`, `Audio/Video`, …).

The three flags in that structure are not 0/1 booleans: Windows stores a distinct bit value in each
(`fConnected` is `0x20`, `fRemembered` `0x10`, `fAuthenticated` `0x08`), so only a non-zero test is
correct.

That search reports nothing at all on a machine whose only peripheral is an LE one, which is not
treated as an error: the LE half still runs, and a genuinely failing `BluetoothFindFirstDevice()` is
told apart from an empty result by `GetLastError() == ERROR_NO_MORE_ITEMS`.

The Low Energy half lives in `bluetooth_windows.cpp` and exists because reaching LE means WinRT,
which is C++ only. It asks `Windows.Devices.Enumeration.DeviceInformation` for
`FindAllAsyncAqsFilterAndAdditionalProperties` with the filter produced by
`BluetoothLEDevice::GetDeviceSelectorFromPairingState(true)`, and asks for five Association Endpoint
properties by name: `System.Devices.Aep.IsConnected`, `SignalStrength`, `DeviceAddress`,
`ContainerId` and `Bluetooth.Le.Appearance`. A name that is not canonical makes the whole call fail
with `0x8002802B`, so the list is exactly the keys that were checked against a live machine.
Requesting `DeviceInformationKind::AssociationEndpoint` instead would return the same data but costs
about **61 seconds per call** because it scans the radio, where the pairing-state selector answers in
**9–22 ms**; the same trap one layer further out is any `Uncached` GATT call, at 7.7 s per
unreachable device.

The two halves are joined on the container id, because that is the only identity they share: a
dual-mode device has one node per enumerator, and the two nodes carry *different* addresses. The LE
side has `System.Devices.Aep.ContainerId`; the classic side has none, so the file walks the
Bluetooth class of the device tree with `CM_Get_Device_ID_ListW()` and builds an
address → container map from `DEVPKEY_Bluetooth_DeviceAddress` and `DEVPKEY_Device_ContainerId`. The
device tree spells those addresses without separators and in a case that depends on the enumerator
(`BTHENUM` upper case, `BTHLE` lower case), so both sides of that comparison are folded. Where a
device is found in both lists the classic entry wins its name, address and type, and gains the LE
bit, the signal quality, and the LE name if the classic one is empty; `connected` is the union of the
two links, which can disagree. An LE device whose container matches nothing is appended as a new
entry, subject to the same `showDisconnected` filter as everything else.

Neither half is redundant, and the LE one cannot replace the classic one. The filter it is built on
is restricted to the LE protocol: it expands to `System.Devices.DevObjectType:=5` and
`System.Devices.Aep.ProtocolId:="{bb7bb05e-5972-42b5-94fc-76eaa7084d49}"` (the LE protocol id; the
classic stack has `{e0cbf06c-cd8b-4647-bb8a-263b43f0f974}`), together with
`IsPaired:=True OR IssueInquiry:=False`. So it
answers "endpoints that were paired over LE", not "devices that speak LE": a dual-mode device paired
over BR/EDR alone has no such endpoint, and it is invisible to that half. The two searches also use
different predicates for the offline case — the classic one reports what is connected, the LE one
what is paired — so neither set contains the other. On a machine with a Bluetooth gamepad, an LE-only
peripheral, a phone and a headset, the device tree holds six nodes (`BTHENUM\Dev_*` for the gamepad,
the phone and the headset; `BTHLE\Dev_*` for the phone, the headset and the LE-only one), the LE pass
returns three of the four devices and the classic pass returns the other three; the device the LE
half misses is the connected gamepad. The classic half is also the side the merge is anchored on —
its addresses are the ones looked up in the device tree — so without it a dual-mode device would be
printed under its LE address rather than the one the operating system shows.

The signal quality is `ffRssiToSignalQuality()` of `System.Devices.Aep.SignalStrength`, and it is
reported whenever the endpoint has that property — including for a disconnected device, because
`IsPresent` is still true and the figure is the last one the radio saw. The type of an LE-only device
is the GAP appearance category, `Appearance >> 6`, looked up in a hand-written table: Windows'
own `BluetoothLEAppearanceCategories` names only the first nineteen categories and has no
`AudioSink`, so it cannot serve as the runtime table. Category `0` becomes `Unknown`.

There is no *devnode* property that could answer "is this connected" instead. `DEVPKEY_DeviceContainer_IsConnected`
and `IsPaired` are absent from every present devnode, and `CM_Get_DevNode_PropertyW()` answers
`CR_NO_SUCH_VALUE` for `PKEY_Devices_Aep_IsConnected`, so walking the device tree with `cfgmgr32`
cannot produce a connection state; `System.Devices.Aep.IsConnected` is reachable through WinRT
alone.

Battery levels are not in either structure, so a third pass looks for
`DEVPKEY_Bluetooth_BatteryLevel` on device nodes — handsets and headsets tend to expose it through a
BR/EDR service node rather than through the Bluetooth device node itself. Despite the class GUID it
passes, this walks every present node of the tree: `CM_Get_Device_ID_ListW()` only honours the filter
string together with `CM_GETIDLIST_FILTER_CLASS`, which is deliberately not set, because the node
carrying the level is not in that class. The address is matched against the collected devices while
skipping the colons and folding the case, and a device no property matches simply keeps `0`.

### FreeBSD, NetBSD and Haiku

The BSDs walk the Netgraph Bluetooth device list with `bt_devenum()`; FreeBSD adds
`bt_devremote_name_gen()` for the remote name, NetBSD uses `dev->devname` directly. Battery, type
and connection state are all left at their defaults, the device type is hard-coded to the classic
bit and the signal quality to "unknown", and `showDisconnected` is ignored.

Haiku asks `Bluetooth::LocalDevice::GetLocalDevice()` — the *local* adapter — and reports it as the
single device in the list, with the device class dumped by `GetDeviceClass()`. Remote devices are
not enumerated at all.
