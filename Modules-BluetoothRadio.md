# BluetoothRadio

> List Bluetooth radios (supported versions, vendors, etc.)

| | |
|---|---|
| Module type | `bluetoothradio` |
| Default order | 60 (only used by `--gen-config`) |
| Module source | `src/modules/bluetoothradio/bluetoothradio.c` |
| Detection source | `src/detection/bluetoothradio/` |

Prints one line per **local** Bluetooth radio — the adapter built into the machine, not the
peripherals it talks to; those belong to `Modules-Bluetooth`. The key is
`Bluetooth Radio (<radio name>)` and the value is the adapter's Bluetooth version and chip vendor:

```
Bluetooth Radio (my-pc): Bluetooth 5.2 (Intel)
```

The name is the adapter's own local name, which on Windows is the computer's Bluetooth name, so the
default key carries the host name.

The version is derived from the LMP version through a fixed table: `0` → `1.0b`, `1` → `1.1`,
`2` → `1.2`, `3` → `2.0`, `4` → `2.1`, `5` → `3.0`, `6` → `4.0`, `7` → `4.1`, `8` → `4.2`, `9` → `5.0`,
`10` → `5.1`, `11` → `5.2`, `12` → `5.3`, `13` → `5.4`, `14` → `6.0`, `15` → `6.1`, `16` → `6.2`,
`17` → `6.3`. A **negative** LMP version means "at least this version": macOS synthesises `-11` for a
controller that advertises LE Audio and `-6` for one that supports GATT, and the line then reads
`Bluetooth 5.2+ (…)`. A value outside `0`–`17` prints the vendor alone, with no version at all.

Radios the platform reports as disabled are skipped, so a machine with a single powered-off adapter
prints nothing but the error `Bluetooth radios found but none enabled`.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `bluetoothradio_linux.c` | BlueZ `org.bluez.Adapter1` properties over the system D-Bus |
| Android | `bluetoothradio_nosupport.c` | Reports "Not supported on this platform" |
| GNU/Hurd | `bluetoothradio_linux.c` | Same file as Linux |
| FreeBSD / MidnightBSD / DragonFly | `bluetoothradio_nosupport.c` | Not supported |
| NetBSD | `bluetoothradio_nosupport.c` | Not supported |
| OpenBSD | `bluetoothradio_nosupport.c` | Not supported |
| Solaris / illumos | `bluetoothradio_nosupport.c` | Not supported |
| Haiku | `bluetoothradio_nosupport.c` | Not supported, even though `Modules-Bluetooth` has a Haiku backend |
| macOS | `bluetoothradio_apple.m` | `IOBluetoothHostController.controllers` plus `system_profiler SPBluetoothDataType` |
| Windows | `bluetoothradio_windows.c` | `bluetoothapis` radio enumeration plus `IOCTL_BTH_GET_LOCAL_INFO` |

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | `Bluetooth Radio (<name>)` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

There is no module-specific option: everything the detector can find is always printed.

## Format string

Run `fastfetch -h bluetoothradio-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{name}` | Radio name — also available in key format |
| `{address}` | Local radio address |
| `{lmp-version}` | Raw LMP version number; `-2147483648` when the platform does not report one |
| `{lmp-subversion}` | Raw LMP subversion number; `-2147483648` when the platform does not report one |
| `{version}` | Version string from the LMP table (`5.2`); empty when the LMP version is outside `0`–`17`. The trailing `+` of the default line is added by the printer, so a negative version renders `5.2` here |
| `{vendor}` | Chip vendor, decoded from the manufacturer ID |
| `{discoverable}` | `true` or `false` |
| `{connectable}` | `true` or `false` |

Only `{name}` carries a `*` in `fastfetch -h bluetoothradio-format`, so it is the only radio field
available in the key format; `{index}`, `{icon}` and `{module-name}` are available as in every
module. `{index}` counts enabled radios only and starts at `1`.

The two raw LMP variables are handed to the format engine unmodified, so a platform that does not
report one prints the module's "no data" marker `-2147483648` instead of nothing. Wrap them in
`{?…}…{?}` when that matters: the format engine counts every value `<= 0` as unset, so the marker, a
virtual `0` and one of macOS's negative versions all hide the block. See the
[Format String Guide](Format-String-Guide#conditional-content) for the exact rule.

## JSON output

```jsonc
{
    "type": "BluetoothRadio",
    "result": [
        {
            "name": "my-pc",
            "address": "AA:BB:CC:DD:EE:FF",
            "lmpVersion": 11,
            "lmpSubversion": 12568,
            "vendor": "Intel",
            "enabled": true,
            "discoverable": false,
            "connectable": true
        }
    ]
}
```

`lmpVersion` and `lmpSubversion` are `null` when the platform did not report them, and disabled
radios stay in this array with `"enabled": false`. On failure the object is
`{ "type": "BluetoothRadio", "error": "…" }`.

## Examples

```jsonc
// Everything the detector knows about the radio
{ "type": "bluetoothradio", "format": "{name}|{version}|{vendor}|{lmp-version}|{lmp-subversion}|{discoverable}|{connectable}" }
```

prints, on a Windows machine with an Intel radio:

```
Bluetooth Radio (my-pc): my-pc|5.2|Intel|11|12568|false|true
```

```jsonc
// Number the radios and drop the noisy default key
{ "type": "bluetoothradio", "key": "{index} {name}" }
```

```
1 my-pc: Bluetooth 5.2 (Intel)
```

```jsonc
// Print the raw subversion only when the platform reports one — the bare variable would
// print the "-2147483648" marker on a platform that never fills it in (Linux never does)
{ "type": "bluetoothradio", "format": "{version} {vendor}{?lmp-subversion} (LMP sub {lmp-subversion}){?}" }
```

The `{?name}…{?}` block prints its contents only when the argument is set, and `{/name}…{/}` only
when it is not. "Set" means greater than `0` for a number, so both tests already do the right thing
for the LMP markers.

## Pitfalls

- **The default key contains the computer name on Windows.** The radio name is the machine's own
  Bluetooth name, so a plain `-s bluetoothradio` line leaks the host name into logs and
  screenshots. Set `key` explicitly when that matters.
- **`{lmp-version}` and `{lmp-subversion}` print `-2147483648` when the platform does not report
  them.** `INT_MIN` is this module's "no data" marker: the JSON writer emits `null` and the default
  line drops the version, but the two format variables are passed the raw `int32_t` and print it.
  This is deliberate rather than an oversight — the format engine's "is it set" test is `value > 0`
  for every numeric type, so `{?lmp-version}…{?}` and `{/lmp-version}…{/}` already treat the marker
  as unset. Use the conditional form instead of the bare variable. Linux never fills either field.
  A negative value that is *not* the marker (macOS invents `-11` / `-6`, see below) is unset for
  `{?}` as well, while the JSON keeps the number — the two outlets answer different questions.
- **`{connectable}` does not mean connectable on Linux.** BlueZ's `Adapter1` interface has no such
  property; the module maps `Pairable` onto it, so the value answers "is this adapter in pairable
  mode", which is a different question.
- **`enabled` is meaningless on Windows and `connectable` on macOS.** The Windows backend sets
  `enabled = true` unconditionally, and the macOS backend sets `connectable = true`
  unconditionally, so a disabled radio or an unreachable controller still reports `true`.
- **A disabled radio disappears from the text output but not from the JSON.** The printer skips
  `enabled == false` entries; `--format json` keeps them. On Linux that is also what happens when
  the BlueZ `Properties.GetAll` call fails: the error is discarded and the adapter silently stays
  disabled.
- **"No Bluetooth radios found or service disabled" covers two different situations.** The Windows
  backend returns that one message both when there is genuinely no radio and when a radio exists but
  the Bluetooth Support Service is stopped. It goes through `ffPrintError()`, so it is invisible
  unless `display.showErrors` is on.
- **macOS depends on an external process.** The backend runs
  `system_profiler SPBluetoothDataType -xml -detailLevel basic`, so the values are only as good as
  that tool's output; a failure there aborts the whole module with
  `Starting \`system_profiler …\` failed`.
- **macOS reports a synthetic LMP version.** It is never read from the controller: `-11` and `-6`
  are invented from the advertised service list, so `{lmp-version}` is a capability hint there, not
  a hardware revision. A controller that advertises neither LE Audio nor GATT keeps the `INT_MIN`
  marker, which `{?lmp-version}…{?}` reports as unset (see above).
- **Battery level is not part of this module.** A radio is the adapter; peripheral battery lives in
  `Modules-Bluetooth`.

## Implementation

`ffDetectBluetoothRadio()` fills a list of `FFBluetoothRadioResult` (`name`, `address`, `vendor`,
`lmpVersion`, `lmpSubversion`, `enabled`, `discoverable`, `connectable`); the printer walks it,
skipping disabled entries, and `ffGenerateBluetoothRadioJsonResult()` serialises all of it. The two
paths call the detector separately. Nothing is cached, so a `--dynamic-interval` run re-probes on
every round.

`printDevice()` builds the key itself — `"<module display name> (<radio name>)"` by default, or the
parsed `key` when one is set — and maps the LMP version to the version string. The vendor name comes
from `ffBluetoothRadioGetVendor()`, a 50-entry table of the Bluetooth SIG manufacturer IDs
(`0` Ericsson … `76` Apple, `65535` Internal Use), returning `Unknown` for anything else.

### Linux

`/sys/class/bluetooth` is listed and every entry whose name contains **no** colon is treated as an
adapter (the entries with colons are connected peripherals). For each one,
`org.freedesktop.DBus.Properties.GetAll` is called on `/org/bluez/<hciName>` with interface
`org.bluez.Adapter1`, and the reply is mapped as `Address` → address, `Alias` → name,
`Manufacturer` → vendor, `Version` → lmpVersion, `Powered` → enabled, `Discoverable` → discoverable
and `Pairable` → connectable. `lmpSubversion` is never assigned. The return value of that call is
ignored, so an adapter that fails to answer keeps `Unknown` as its vendor and stays disabled.

### macOS

`IOBluetoothHostController.controllers` supplies one entry per controller: name and address come
from the controller object, `enabled` from `powerState == kBluetoothHCIPowerStateON`, and
`connectable` is hard-coded. `lmpVersion` starts at `INT_MIN` and becomes `-11` or `-6` when
`system_profiler`'s `controller_supportedServices` string contains ` LEA ` or ` GATT `.
`discoverable` is `controller_discoverable != "attrib_off"`, and the vendor is taken from the
parenthesised part of `controller_vendorID` — the text between `(` and `)`.

### Windows

`BluetoothFindFirstRadio()` / `BluetoothFindNextRadio()` / `BluetoothFindRadioClose()` are loaded
from `bluetoothapis.dll` and each radio handle is queried with `IOCTL_BTH_GET_LOCAL_INFO`, which
fills a packed 292-byte `BTH_LOCAL_RADIO_INFO`. The name and address come from `localInfo`, the
vendor and both LMP numbers from `radioInfo`, and `BluetoothIsConnectable()` /
`BluetoothIsDiscoverable()` from the same module supply the two flags. `enabled` is set
unconditionally, and there is no discovery: the API only reports radios the system already knows.
A radio whose `DeviceIoControl()` call fails is skipped with `continue`, which also skips the
`NtClose()` on its handle.
