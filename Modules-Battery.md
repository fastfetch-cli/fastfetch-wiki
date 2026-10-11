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
| Android | `battery_android.c` | `batteryproperties` (IBatteryPropertiesRegistrar) and `batterystats` (IBatteryStats) over `/dev/binder`, the `debug.tracing.*` system properties for the charger bits and the charging state, plus `/sys/class/thermal` for the temperature; `dumpsys battery` instead for the shell UID and root. See [Android Permissions](Android-Permissions) |
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
  every platform, because it either costs an extra SMC/kstat round-trip or a walk over the thermal
  zones.
- **On Android the route depends on the UID, and the answer with it.** An app UID goes over
  `/dev/binder`: `IBatteryPropertiesRegistrar` for the capacity, `IBatteryStats` for the remaining time,
  and the kernel's thermal zones for the temperature. The charger bits and the charging state come from
  a pair of system properties `BatteryService` publishes and any UID may read — `debug.tracing.plug_type`
  and `debug.tracing.battery_status` — so an app UID gets the `AC / USB / wireless` bits too. The shell
  UID and root get `dumpsys battery` first, because the fork/exec is worth paying there for the
  technology and the critical capacity level, which are the two things left that nothing else answers.
  The manufacturer, the model name, the serial number, the manufacture date and the cycle count stay
  empty on both routes.
- **On Android the remaining time is only reported for an app UID.** The shell UID and root take the
  `dumpsys battery` route, which answers and returns before the `IBatteryStats` call that computes the
  estimate — and the dump has no such line.
- **The technology and the cycle count live in the battery broadcast, and only one of the two is
  reachable.** `ACTION_BATTERY_CHANGED` is sticky and needs no permission: it carries the technology,
  and a cycle count since Android 14 as `EXTRA_CYCLE_COUNT`. Reading it means calling
  `registerReceiver`, which only a Java side can do, so every route to that broadcast is a Java
  process — `dumpsys battery` is one and is used for the shell UID and root, `termux-api` is another
  and is not used because it hangs. The cycle count is not in the dump, so it stays empty everywhere.
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

### Android

The route depends on the UID, and that is deliberate. `dumpsys battery` needs
`android.permission.DUMP`, which only the shell UID and root hold: an app UID is answered with
`Can't find service: battery` on stdout and a zero exit status, so forking it there costs a child
process and cannot succeed. Where it is allowed to run it goes first, because it is the richer of the
two — a flat `key: value` list read by name, so it carries what neither the registrar nor a property has
(the technology and the critical capacity level) and neither a vendor that prints extra keys in the
middle nor a release that appends a field can break it. The reply of the other route is positional and
breaks silently instead.

That other route opens `/dev/binder` through `common/android/binder.h`, resolves the
`batteryproperties` service in system_server, and calls `IBatteryPropertiesRegistrar.getProperty` for
`BATTERY_PROPERTY_CAPACITY` (4) — that one is the route. The status costs no transaction at all, because
`BatteryService` mirrors it into a world-readable system property as it processes each update:
`debug.tracing.battery_status` carries the same `mHealthInfo.batteryStatus` a `getProperty` call would
answer with, so `BATTERY_PROPERTY_STATUS` (6) is only reached when that property is missing or holds
something other than a `BATTERY_STATUS_*` value.

The other property in that pair is where the charger bits come from, and it is the reading the registrar
does not have at all: `debug.tracing.plug_type`, also written by `BatteryService.processValuesLocked`,
holds the `BATTERY_PLUGGED_*` bits — 1 AC, 2 USB, 4 wireless, and 0 when nothing is attached; the dock
bit (8) has no `FF_BATTERY_STATUS_*` counterpart and is dropped. Neither property is a `BatteryProperty`,
so nothing about the registrar's positional reply applies to them, and both are read pessimistically: a
device that does not publish them loses the charger bits and falls back to the service for the status,
which is what a `dumpsys`-only release looks like.

The transaction code is read out of the device's own `framework.jar` at run time, by the name of the
constant the dex carries (`TRANSACTION_getProperty`), because the number has moved already: Android 10
dropped the two listener methods ahead of it, which moved `getProperty` from the third position to the
first. The reply is positional (`[exception][return value][value present][int64 mValueLong]`) and is
not negotiated with the service, so a wrong code or a reply whose fields have drifted shows up as a
wrong capacity rather than as a failure — `ffBinderReadU64` returns 0 for a read past the end.

The remaining time is not a property of the pack: it is a forecast `BatteryStatsService` makes from the
discharge history, so it comes from a second service, `batterystats`, whose interface is
`com.android.internal.app.IBatteryStats`. Its `computeBatteryTimeRemaining` takes no argument and
answers with one `long`, so its code is read out of the same jar by the name of its own constant, and
its reply has no out-parameter marker in front of the value. That number is not stable either, and less
so: the same method is transaction 19 on an Android 16 device, 22 on an Android 14 one and 20 on an
Android 10 one, which is what a hard-coded code would have to survive. The answer is in milliseconds
and is divided down to the seconds `FFBatteryResult` carries; anything at or below zero is how the
interface says it has no estimate, and leaves the field unknown. This is the one part of the route
allowed to be missing — a jar without the method, a service that is not running or an answer of `-1`
all cost the estimate and nothing else, because it is asked for last. It is also part of the binder
route alone: the `dumpsys` route has no such line and returns before this one runs, so the shell UID
and root get no estimate.

Neither interface declares which of its methods an app UID may call, and the wall is inside
`BatteryStatsService`, which lives in `services.jar` and is not there to read. It was therefore
measured, as an app UID: `computeBatteryTimeRemaining`, `computeChargeTimeRemaining`, `isCharging` and
the two `getAllWakeLocks` calls answer with no permission at all, while the read-only statistic getters
(`getAwakeTimeBattery`, `getAwakeTimePlugged` and the cellular / wifi / gps / bluetooth ones) come back
with `Access denied, requires: android.permission.BATTERY_STATS`.

The registrar is not a source of the other fields at all, rather than a source behind a permission:
`BatteryProperty` has no temperature, no technology and no cycle count, and manufacturer and model name
are not battery data. Of the properties it does have, the ones Android 15 added (ids 7 to 12) are
behind `BATTERY_STATS` and ids 1 to 6 are not — which is why the capacity and the status are readable
without a permission and the rest is not read. The module therefore fills the capacity, the status bits
and the time estimate, and leaves everything else unset.

Two of those fields are not unobtainable, and the difference is worth keeping straight. The technology
and a cycle count ride `ACTION_BATTERY_CHANGED`, a sticky broadcast that is read without a permission
— the cycle count since Android 14, as `EXTRA_CYCLE_COUNT`. What is out of reach is the call that
fetches it: a sticky broadcast is read by `registerReceiver`, the NDK does not wrap it, and a native
binary has no Java side to call it from, so every route to that broadcast is a Java process. That is
the fork/exec the shell UID and root pay for the dump, which is where the technology comes from on that
route; the cycle count is in neither. `termux-api` is a third route to the same broadcast and a worked
example of why it is not used: it answers, and on the device this was measured on it hung on six of six
runs, each killed at 30 seconds having written nothing, after exec'ing `am broadcast` and waiting on a
socket with no timeout of its own. The manufacturer and the model name are not battery data at all —
they are `Build.MANUFACTURER` and `Build.MODEL`.

The whole route costs about 4.6 ms on the device this was measured on, most of it the walk over the jar
for the two transaction codes rather than the two transactions themselves. The two classes are not in
the same dex entry — `IBatteryPropertiesRegistrar$Stub` is in `classes3.dex` and `IBatteryStats$Stub`
in `classes5.dex` — so the walk to the second one is where about 1.3 ms of that goes.

The temperature comes from neither service. The kernel publishes it as a thermal zone, and the module
reads the zone Android's thermal HAL names `battery` out of `/sys/class/thermal` — only when `temp` is
true, because that directory holds a hundred-odd entries on the device this was measured on and the
walk costs about 1.3 ms. The `dumpsys` route has a `temperature:` line of its own, in tenths of a
degree, and that one wins where the dump runs, so the walk is only paid on the app route.

The zone is looked up, never addressed. Its number is a property of the probe order rather than of the
hardware — the battery is `thermal_zone67` on one device and need not be on the next — so the directory
is walked and each entry is matched on the `type` it reports. That match is an exact string and has to
stay one. A prefix would be cheaper and wrong: the same directory holds hardware trip points
(`cpu-hw-trip-0` reports 95000, a perfectly believable 95 degrees), current and battery levels
(`pmih010x-ibat-lvl0`, 0 or a small integer) and raw registers (`vbat`, in millivolts). None of those
are a temperature and every one of them would be printed as one, so a device that names its battery
zone something else reports no temperature rather than a wrong one.

`/sys/class/hwmon` and `/sys/class/power_supply` are not alternatives to it: both are closed to an app
UID outright, directory and all. An `O_PATH` open of the class directory succeeding says nothing
either, because `O_PATH` does not check read permission — it is the `openat` that follows which fails.

### Linux

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
