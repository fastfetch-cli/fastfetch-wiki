# CPU

> Print CPU name, frequency, etc.

| | |
|---|---|
| Module type | `cpu` |
| Default order | 33 (only used by `--gen-config`) |
| Module source | `src/modules/cpu/cpu.c` |
| Detection source | `src/detection/cpu/` |

Prints the processor model, its core counts, its maximum clock and — if you ask for it — its
package temperature. All of it goes on one line.

```
CPU: Apple M<n> (8+2) @ 3.23 GHz
CPU: Intel(R) Core(TM) i7-9750H (6) @ 2.60 GHz - 52.0°C
CPU: AMD Ryzen 7 7840U (8) @ 5.10 GHz
CPU: Virtualized Apple Silicon (5)
```

The `(8+2)` clause is `showPeCoreCount` at work: cores are grouped by clock and the group sizes are
joined with `+`. With the option off the same machine prints `(10)` instead. The `@` clause prefers
the maximum clock and falls back to the base clock, and disappears entirely when both are `0` —
which is the normal case on Apple Silicon and inside virtual machines. The `- 52.0°C` suffix needs
`temp` to be enabled *and* the platform to have a sensor that answers.

## Platform support

| Platform | Implementation | Data source |
|---|---|---|
| Linux | `cpu_linux.c` (+ `cpu_x86.c` on x86) | `/proc/cpuinfo`, `/sys/devices/system/cpu`, CPUID or `/proc/self/auxv` |
| Android | `cpu_linux.c` | The Linux path, plus SoC-name lookups from system properties |
| FreeBSD | `cpu_bsd.c` | `sysctl` OIDs |
| NetBSD | `cpu_nbsd.c` | `sysctl`, plus the `sysmon` device for the temperature |
| OpenBSD | `cpu_obsd.c` | `sysctl`, plus the `hw.sensors` array for the temperature |
| Solaris / illumos | `cpu_sunos.c` | `kstat`, plus `ipmitool` as a temperature fallback |
| Haiku | `cpu_haiku.c` | `get_system_info()` and `get_cpu_topology_info()` |
| GNU/Hurd | `cpu_linux.c` | The same file as Linux |
| macOS | `cpu_apple.c` | `sysctl`, plus IOKit and the SMC for the clock and the temperature |
| Windows | `cpu_windows.c` | The registry, `NtQuerySystemInformationEx`, SMBIOS and Perflib |

All ten platforms have a real implementation. `cpu_x86.c` — a large table of Intel and AMD code
names and process technologies — is listed once in the shared part of `CMakeLists.txt` rather than
in a platform block, so it is compiled everywhere; its whole body sits behind
`#if __x86_64__ || __i386__`, so on ARM it contributes nothing.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `temp` | boolean or object | `false` | Detect and print the package temperature. The object form sets the colour thresholds: `{ "green": 60, "yellow": 80 }`. |
| `tempSensor` | string | – | Sensor to read instead of the automatic one. The accepted syntax is platform-specific. |
| `showPeCoreCount` | boolean | `true` | Group the core count by clock (`8+2`) and fill `{core-types}`. |
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

`temp` and `tempSensor` are not specific to this module: the same `ffTempsParseJsonObject()` helper
parses them for `gpu`, `battery` and `physicaldisk` too, so the semantics are identical everywhere.
`temp` additionally accepts `null` (meaning `false`) and `{}` (meaning `true` with the default
thresholds).

`tempSensor` means something different on each platform:

| Platform | Accepted value | Example |
|---|---|---|
| Linux / Android | `hwmonN`, `thermal_zoneN`, `cputemp.N`, or an absolute path | `hwmon0`, `thermal_zone0` |
| macOS | an SMC sensor key | `Tp01` |
| Windows | a Perflib thermal-zone instance name | `\_TZ.CPUZ` |
| FreeBSD | a `sysctl` OID | `dev.cpu.0.temperature` |
| NetBSD | a `sysmon` sensor key | `coretemp0` |
| OpenBSD | a `sensordev` name | `cpu0` |
| Solaris | a kstat module name | `cpu_temp` |

## Format string

Run `fastfetch -h cpu-format` for the authoritative list. That list is generated from the module,
so it is two entries longer on x86 than it is on ARM.

| Variable | Description |
|---|---|
| `{name}` | Model name |
| `{vendor}` | Vendor |
| `{cores-physical}` | Physical core count |
| `{cores-logical}` | Logical core count |
| `{cores-online}` | Online core count |
| `{freq-base}` | Base clock, formatted (`2.60 GHz`) |
| `{freq-max}` | Maximum clock, formatted |
| `{temperature}` | Temperature, formatted and coloured |
| `{core-types}` | Core count grouped by clock (`8+2`); empty unless `showPeCoreCount` |
| `{packages}` | Processor package count |
| `{march}` | Microarchitecture (`x86_64-v3`, `ARMv8.4-A`) |
| `{numa-nodes}` | NUMA node count |
| `{code-name}` | CPU code name — **x86 only** |
| `{technology}` | Process technology — **x86 only** |

`{freq-base}` and `{freq-max}` go through `ffFreqAppendNum()`, which uses `display.freq.ndigits`
(default `2`). A clock of `0` renders as an empty string, not as `0 GHz`. `{temperature}` goes
through `ffTempsAppendNum()`, which uses `display.temp.*` for the unit and the digit count and this
module's `temp.green` / `temp.yellow` for the colour.

## JSON output

```jsonc
{
    "type": "CPU",
    "result": {
        "cpu": "Apple M<n>",
        "vendor": "Apple",
        "packages": 1,
        "cores": { "physical": 10, "logical": 10, "online": 10 },
        "frequency": { "base": 0, "max": 3228 },
        "coreTypes": [
            { "count": 8, "freq": 2 },
            { "count": 2, "freq": 1 }
        ],
        "temperature": null,
        "march": "ARMv8.4-A",
        "numaNodes": null
    }
}
```

`frequency.base` and `frequency.max` are in MHz, not in GHz — the text path is what divides by
1000. `coreTypes[].freq` is *not* a clock: it is the performance level the platform reported, and a
larger number means a faster core class. `temperature` is the raw double, with no rounding applied;
the text path is what applies `display.temp.ndigits`. `packages`, `temperature`, `march` and
`numaNodes` are `null` when unknown, and `codeName` / `technology` are absent from the object
entirely on non-x86 builds rather than being `null`.

## Examples

```jsonc
{ "type": "cpu", "format": "{name} ({cores-logical}) @ {freq-max}" }
```

```jsonc
{ "type": "cpu", "temp": { "green": 40, "yellow": 70 } }
```

```jsonc
{ "type": "cpu", "showPeCoreCount": false }
```

## Pitfalls

- **`showPeCoreCount` defaults to `true`.** Measured on a hybrid machine: with the key absent the
  output is `Apple M<n> (8+2)`, with `false` it is `Apple M<n> (10)`, with `true` it is `(8+2)`
  again. There is no build-time switch behind it — `ffInitCPUOptions()` simply sets it to `true` —
  so a config that omits the key still gets the per-core-class grouping.
- **`{code-name}` and `{technology}` do not exist on non-x86 builds.** They are behind
  `#if __i386__ || __x86_64__` in three places — the result struct, the format-argument list and the
  JSON writer. On ARM a format string containing them leaves the placeholder visible, and the JSON
  object has no `codeName` / `technology` keys at all.
- **An unknown JSON key is a warning, not a failure.** `{ "type": "cpu", "bogus": 1 }` prints
  `CPU: Unknown JSON key bogus` and then prints the CPU line normally, with exit code 0. The same
  applies to the removed `freqNdigits`, which prints
  `CPU: modules.CPU.freqNdigits has been moved to display.freq.ndigits` and is otherwise ignored.
  Neither message appears at all under the JSON config path unless `display.showErrors` is on.
- **The model name is rewritten before it is printed.** A fixed list of suffixes is removed
  (` CPU`, ` Processor`, ` Dual-Core`, ` 16-Core`, …), everything from ` w/ Radeon ` or
  ` with Radeon ` onwards is cut, and everything from the first `@` onwards is cut. A CPU whose
  marketing name genuinely contains one of those substrings will be printed without it.
- **`temp: true` can silently do nothing.** Haiku has no temperature support at all — the option is
  accepted and the field stays unset, so `{temperature}` is empty and the JSON value is `null` with
  no error. The same silent outcome happens on any platform whose sensor read fails.
- **Swapping `green` and `yellow` inverts the colour scale rather than being rejected.**
  `ffTempsAppendNum()` compares the two thresholds and switches to a "lower is hotter" rule when
  `green > yellow`. A typo turns a "warmer is redder" gradient into the opposite, with no warning.
- **`temp` makes the module scan the filesystem.** On Linux an enabled temperature walks
  `/sys/class/hwmon/`, `/sys/class/thermal/` and `/sys/devices/platform/` on every round looking for
  the first sensor that answers. Leaving `temp` off keeps the module to `/proc/cpuinfo` and a couple
  of `sysctl`s.
- **The x86 microarchitecture label is a feature-level guess, not a hardware query.** `x86_64-v1`
  through `x86_64-v4` are derived from CPUID feature bits plus an `XGETBV` check that the OS
  actually enabled the wider register state. A CPU that supports AVX-512 but runs under a hypervisor
  that does not expose it reports the lower level.
- **The ARM microarchitecture label is deliberately approximate.** The code maps CPUID/`HWCAP`
  feature sets onto `ARMv8.0-A` … `ARMv9.6-A`, and the source comments note that the mapping is
  imperfect — for instance Apple's first M-series part is listed as ARMv8.5-A elsewhere but lacks
  BTI, so fastfetch reports ARMv8.4-A. Treat `{march}` as "at least this level", not as an exact
  identification.
- **A single-core machine reports nothing but the name.** The module treats
  `vendor == "" && name == "" && coresOnline <= 1` as "no CPU detected" and prints an error instead
  of a result.

## Implementation

`ffPrintCPU()` and `ffGenerateCPUJsonResult()` both call `ffDetectCPU()` with no cache, so under
`--dynamic-interval` the whole detection runs again every round.

`ffDetectCPU()` in `src/detection/cpu/cpu.c` is a thin wrapper: it calls the platform's
`ffDetectCPUImpl()`, then normalises the model name (the suffix removal and the two truncations
described above), then calls `ffCPUDetectX86Specific()` on x86 to fill `codeName` and `technology`
from the code-name table.

`ffCPUDetectByCpuid()` is the other shared helper. On x86 it reads CPUID leaf `0x16` for base and max
frequency — skipping any zero, which is what a hypervisor returns — and derives `march` from leaf 1
and leaf 7 feature bits plus `XGETBV`. On Linux ARM it parses `/proc/self/auxv` for `AT_HWCAP` and
`AT_HWCAP2`; on macOS ARM it reads the 80-bit `hw.optional.arm.caps` mask; on Windows ARM it calls
`IsProcessorFeaturePresent()`. On every other architecture it does nothing, which is why `{march}`
is empty there.

### Linux and Android

On x86, `/proc/cpuinfo` supplies the name, the vendor, the cores-per-package count and the `cpu MHz`
figure; `get_nprocs_conf()` and `get_nprocs()` supply the logical and online counts; the package
count is the number of distinct `physical id` values, and `coresPhysical` is cores-per-package
multiplied by that. `frequencyBase` and `frequencyMax` come from
`/sys/devices/system/cpu/cpufreq/policyN/`, preferring `cpuinfo_max_freq` over `scaling_max_freq`
and `base_frequency` for the base, with the `cpu MHz` value as a last resort. NUMA nodes are counted
by listing `/sys/devices/system/node/` for entries named `nodeN`.

On ARM and the remaining architectures the same `/proc/cpuinfo` parser is compiled with different
keys (`Hardware :` on ARM, `cpu model :` on MIPS, `isa :` and `uarch :` on RISC-V, and so on), the
vendor is derived from the `CPU implementer` field, and `coresPhysical` is counted from
`/sys/devices/system/cpu/cpuN/topology/physical_package_id`. Android additionally reads system
properties to turn a SoC code into a marketing name, with dedicated tables for Qualcomm, MediaTek
and Exynos parts.

The temperature walks `/sys/class/hwmon/` looking for a directory whose `name` file looks like a CPU
sensor, then falls back to `/sys/class/thermal/thermal_zoneN/temp` and
`/sys/devices/platform/cputemp.N/`. An explicit `tempSensor` short-circuits all of that and reads
the one path you named, accepting either a directory (where it then looks for `temp1_input`, or
`temp` for a thermal zone) or a file.

### FreeBSD

`hw.model` gives the name, `hw.ncpu` / `kern.smp.cores` / `kern.smp.cpus` give the core counts, and
`kern.sched.topology_spec` is scanned for `</group>` to count packages. `hw.clockrate` and the
per-CPU `dev.cpu.N.freq_levels` strings (which are `MHz/watts` pairs) give the clocks. The
temperature comes from `dev.cpu.0.temperature` or `hw.acpi.thermal.tz0.temperature`, in tenths of a
Kelvin. On x86 the process is briefly pinned to cores 1 and 2 with `cpuset_setaffinity()` before
reading CPUID, on the assumption that those are performance cores.

### NetBSD and OpenBSD

NetBSD tries `machdep.cpu_brand`, `machdep.dmi.processor-version`, `hw.cpu0.name` and `hw.model` in
turn, and reads the temperature from the `sysmon` device's property dictionary, preferring
`coretemp0`, then `amdzentemp0`, `viac7temp0` and `acpitz0`. OpenBSD takes the name from `hw.model`
and walks the `hw.sensors` array looking for a `SENSOR_TEMP` entry whose device name starts with
`cpu`, skipping anything flagged `SENSOR_FINVALID`.

### Solaris and illumos

`kstat` provides everything: the `cpu_info` module for `brand`, `vendor_id` and `clock_MHz`, the
`unix:0:system_misc` module for `ncpus`, and `chip_id` / `core_id` value counts for the package and
core counts. The temperature is looked up first in the `temperature`, `cpu_temp` and
`acpi_thermal` kstats and then, if that fails, by running `ipmitool -c sdr list` and matching
`CPU<n> Temp,<value>,degrees C,ok`.

### Haiku

`get_system_info()` gives the online core count and `get_cpu_topology_info()` gives the topology
tree, which is walked for `B_TOPOLOGY_PACKAGE` (packages) and `B_TOPOLOGY_CORE` (cores, and the
highest `default_frequency` seen). The model name is resolved from the platform, vendor and model
numbers through Haiku's own `get_cpu_model_string()`. There is no temperature path.

### macOS

`machdep.cpu.brand_string` gives the name; on Apple Silicon the vendor is filled in as `Apple` when
the name starts with `Apple `. The four core counts come from `hw.physicalcpu_max` /
`hw.physicalcpu`, `hw.logicalcpu_max` / `hw.ncpu` and `hw.logicalcpu` / `hw.activecpu`, each pair
falling back when the first reads `1`. The `(8+2)` grouping comes from `hw.nperflevels` and
`hw.perflevelN.logicalcpu`, with the performance level stored in `coreTypes[].freq` as a
descending index rather than a clock.

The clock differs by architecture. On Apple Silicon it reads the `voltage-states5-sram` property of
the `pmgr` IOKit service — an array of frequency/voltage pairs, of which the highest frequency is
taken — and guesses the unit by magnitude, treating values above 100000000 as Hz and everything
else as kHz. On Intel it reads `hw.cpufrequency` and `hw.cpufrequency_max`.

The temperature reads the SMC. Without `tempSensor` it picks a sensor set from the model name:
the number after `Apple M` selects the M1 through M5 table, and anything else (Intel, or a name
that does not parse) falls back to the x86 table. An unrecognised Apple Silicon generation returns
`Unsupported Apple Silicon CPU`, which leaves the temperature unset.

### Windows

`NtQuerySystemInformationEx(SystemLogicalProcessorAndGroupInformation, RelationAll)` supplies the
core counts, the package count, the NUMA node count and — when `showPeCoreCount` is on — the
`coreTypes` grouping, keyed on the processor's `EfficiencyClass`. The name, vendor and base clock
come from the registry key
`HKEY_LOCAL_MACHINE\HARDWARE\DESCRIPTION\System\CentralProcessor\0`. If CPUID did not produce a
maximum clock, SMBIOS type 4 is consulted, but its value is discarded when it is below the base
clock or more than twice the base clock — the code comments call that an invalid report. The
temperature comes from the Perflib `Thermal Zone Information` counter set, defaulting to the
`\_TZ.CPUZ` instance and falling back to the first instance that exists.
