# Battery

> Print battery information

| | |
|---|---|
| Module type | `battery` |
| Default order | 44 (only used by `--gen-config`) |
| Module source | `src/modules/battery/battery.c` |
| Detection source | `src/detection/battery/` |

Prints every battery the system reports, plus the AC/USB/wireless power source state, the
remaining time and (optionally) the battery temperature. One line is printed per battery.

The default key is `Battery (<model name>)`, or plain `Battery` when the model name is
unknown. A custom `key` is itself a format string and may use `{index}`, `{name}`,
`{icon}` and `{module-name}`; `{index}` is `0` for a single battery and `1`, `2`, … when
several batteries are present.

Default output looks like:

```
Battery (LION-4C20): 100% [AC Connected] - 31.2°C
```

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `battery_linux.c` | `/sys/class/power_supply/*` (sysfs) |
| Android | `battery_android.c` | `/sys/class/power_supply/*` plus Android-specific sysfs nodes |
| macOS | `battery_apple.c` | IOKit `AppleSmartBattery` registry entry (+ SMC for temperature) |
| Windows | `battery_windows.c` | WMI battery classes (`batclass.h`) |
| FreeBSD / MidnightBSD / DragonFly | `battery_bsd.c` | `hw.acpi.battery.units` sysctl + `ACPI_IOCTL_BATTERY` ioctl on `/dev/acpi` |
| NetBSD | `battery_nbsd.c` | `/dev/sysmon` + proplib `acpibat*` dictionaries |
| OpenBSD | `battery_obsd.c` | APM ioctl on `/dev/apm` |
| Solaris / illumos | `battery_sunos.c` | kstat |
| Haiku | `battery_haiku.c` | `/dev/power/acpi_battery/` + `acpi_battery_info` ioctl |
| GNU/Hurd | `battery_nosupport.c` | Reports `Not supported on this platform` |


## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `temp` | boolean \| object | `false` | Detect and display the battery temperature. Also accepts a color-range object (`{ "green": 60, "yellow": 80 }`) to color the value. |
| `percent` | object | `{ "green": 50, "yellow": 20, "type": 0 }` | Color thresholds for the capacity output. |
| `key` | string | module name | Module key. A single space hides it. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | The icon printed when `display.key.type` includes the icon bit. Set it to any glyph you like, or to `""` to print none. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

The default `percent` uses `green: 50 > yellow: 20`, i.e. the *inverted* interpretation:
50–100 % is green, 20–50 % is yellow and 0–20 % is red. The exact semantics of the two
thresholds are documented in
[Configuration](Configuration#displaypercenttype).


## Format string

Run `fastfetch -h battery-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{manufacturer}` | Battery manufacturer |
| `{name}` | Battery model name (also available in the key format) |
| `{technology}` | Battery technology (e.g. `Lithium`) |
| `{capacity}` | Capacity percentage, formatted as a number |
| `{capacity-bar}` | Capacity percentage, formatted as a bar |
| `{status}` | Status list, e.g. `AC Connected, Charging` |
| `{temperature}` | Temperature, formatted with the `display.temp` settings |
| `{cycle-count}` | Cycle count |
| `{serial}` | Serial number |
| `{manufacture-date}` | Manufacture date, `YYYY-MM-DD` |
| `{time-days}` / `{time-hours}` / `{time-minutes}` / `{time-seconds}` | Remaining time, split into components |
| `{time-formatted}` | Remaining time, formatted by `display.duration` |


## JSON output

```jsonc
{
    "type": "battery",
    "result": [
        {
            "capacity": 100.0,
            "manufacturer": "Apple Inc.",
            "manufactureDate": "",
            "modelName": "LION-4C20",
            "technology": "Lithium",
            "serial": "0123456789ABCDEF",
            "temperature": 31.15,
            "cycleCount": 42,
            "timeRemaining": null,
            "status": ["AC Connected"]
        }
    ]
}
```

`temperature` and `timeRemaining` are `null` when unknown.


## Examples

```jsonc
{
    "type": "battery",
    "key": "Battery",
    "temp": true,
    "format": "{capacity} {status} ({time-formatted})"
}
```

```jsonc
{
    "type": "battery",
    "format": "{name}: {capacity-bar} {capacity} - {temperature}",
    "percent": { "type": 3, "green": 30, "yellow": 15 },
    "temp": { "green": 45, "yellow": 55 }
}
```


## Pitfalls

- **`{temperature}` is empty unless `temp` is `true`.** Temperature detection is opt-in on
  every platform, because it either costs an extra SMC/kstat round-trip or needs privileges.
- **`{time-formatted}` / `{time-*}` are only filled while discharging.** While charging or on
  AC the remaining time is unknown (`-1`) and the variables expand to `0`.
- **Reading `capacity` can be slow on some Linux laptops** — the source carries an explicit
  "this is expensive" note; the value is read once per battery per run.
- **The percentage is not the "battery health".** It is `current / max`, so a worn battery
  still reports 100 % when fully charged.
- **OpenBSD reports very little.** `{manufacturer}`, `{name}` and `{technology}` are always
  empty and `temp` has no effect.
- **Multiple batteries print multiple lines.** The default key does *not* contain an index,
  so two identical models produce two identical keys. Use a custom key such as
  `"Battery {index}"` to tell them apart.
- **`AC Connected` is a status flag, not a separate row.** On Linux it is inferred from the
  `Mains` power supply, and it is applied to all detected batteries.
- **The default `percent` thresholds are inverted** (`green` > `yellow`): high capacity is
  green, low capacity is red. Setting `green` below `yellow` flips the meaning to
  "low value is good".


## Implementation

### Linux and Android

Enumerates `/sys/class/power_supply/` with `readdir` + `openat(O_PATH | O_DIRECTORY)` so the
directory handle stays valid while reading. For each entry the following files are read
relative to that directory handle:

- `type` — must be `Battery`; an entry of type `Mains` is not a battery, but its `online` file
  is used to mark *every* battery as `AC Connected`.
- `present` — skipped if `0`.
- `scope` — skipped if it is `Device` (these are USB gadget/battery-charger devices, not batteries).
- `capacity` — the only mandatory file; the entry is discarded if it cannot be read.
- `manufacturer`, `model_name`, `technology`, `serial_number`, `capacity_level`,
  `cycle_count`, `status`, `manufacture_{year,month,day}`.
- `temp` — only read when `temp` is `true`; the sysfs value is in tenths of a degree, so it is
  divided by 10.

`status` maps to flags: `Discharging` / `Charging` / `Unknown`; `capacity_level: Critical`
adds `Critical`. Remaining time is taken from `time_to_empty_now` when present, otherwise it
is derived from `charge_now * 3600 / |current_now|`.

An Asahi-Linux machine exposes the battery as `macsmc-battery`, which has no `manufacturer`
file, so `Apple Inc.` is hard-coded for that ID.

The sysfs ABI is documented at
<https://www.kernel.org/doc/Documentation/ABI/testing/sysfs-class-power>.

### macOS

`IOServiceGetMatchingServices(IOServiceMatching("AppleSmartBattery"))` followed by
`IORegistryEntryCreateCFProperties` for every matching registry entry:

- `MaxCapacity` / `CurrentCapacity` → `capacity` (as a percentage).
- `DeviceName`, `Serial`, `Manufacturer`, `CycleCount`.
- `ExternalConnected` → `AC Connected`, otherwise `Discharging` plus `AvgTimeToEmpty`
  (minutes → seconds; `0xFFFF` and negative values mean "unknown").
- `IsCharging` → `Charging`, `AtCriticalLevel` → `Critical`.
- `built-in` → fills in `Apple Inc.` / `Lithium` / `Built-in` when the keys are missing.
- `ManufactureDate` is a packed SBDS value (5 bits day, 4 bits month, 7 bits year since 1800).
  Apple Silicon instead stores it as a string inside the `BatteryData` dictionary, which is
  parsed as `YY MM DD` with an 8-year offset.
- Temperature comes from `kIOPMPSBatteryTemperatureKey` (tenths of Kelvin) and falls back to
  the SMC `TB0T`-style sensor via `ffDetectSmcTemps()`.

### Windows

Enumerates battery devices and queries the four WMI battery classes declared in `batclass.h`:
`BATTERY_STATIC_DATA_WMI_GUID` (manufacturer, model, serial, chemistry, manufacture date),
`BATTERY_STATUS_WMI_GUID` (power state, remaining capacity, charge rate),
`BATTERY_RUNTIME_WMI_GUID` (`EstimatedRuntime`) and
`BATTERY_FULL_CHARGED_CAPACITY_WMI_GUID` (full-charged capacity, used as the percentage base).
Sentinel values such as `BATTERY_UNKNOWN_CAPACITY` / `BATTERY_UNKNOWN_TIME` are treated as
"unknown" rather than converted.

### BSD family

- **FreeBSD**: `sysctlbyname("hw.acpi.battery.units")` gives the count, then
  `ioctl(fd, ACPI_IOCTL_BATTERY, &battio)` on `/dev/acpi` yields `battinfo` (capacity, state,
  minutes left) and `bif`/`bix` (OEM info, model, type, serial, cycle count). `bix` is
  preferred over `bif` when the ACPI extended battery info is available.
- **NetBSD**: opens `/dev/sysmon` and walks the proplib device tree, keeping only keys that
  start with `acpibat`. Capacity is `charge / capacity * 100`; remaining time is
  `charge / discharge_rate * 3600`.
- **OpenBSD**: `ioctl(fd, APM_IOC_GETPOWER, &info)` on `/dev/apm`. Only capacity, state and
  minutes left are available — manufacturer, model and technology are always empty, and the
  temperature is never reported.

### Solaris / illumos / Haiku

Solaris reads the `battery` kstat module through libkstat. Haiku opens
`/dev/power/acpi_battery/`, calls the `acpi_battery_info` and `acpi_extended_battery_info`
ioctls and computes `capacity = basic.capacity * 100 / extended.last_full_charge`.
