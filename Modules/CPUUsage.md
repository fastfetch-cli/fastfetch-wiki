# CPUUsage

> Print CPU usage. Collecting data takes some time

| | |
|---|---|
| Module type | `cpuusage` |
| Default order | 35 (only used by `--gen-config`) |
| Module source | `src/modules/cpuusage/cpuusage.c` |
| Detection source | `src/detection/cpuusage/` |

Prints how busy the CPU is, as an average over a sampling window. The key is `CPU Usage` by
default.

```
CPU Usage: 42%
```

With `separate` the average is replaced by one percentage per logical core, in the order the
platform reports them:

```
CPU Usage: 100% 100% 45% 30% 15% 5% 19% 0% 5% 0%
```

This module is the reason fastfetch has a prepare pass: it samples the CPU counters as early as it
can, then does all the other work, and by the time the module is printed the sampling window has
usually already elapsed. In a default run the module therefore costs close to nothing.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `cpuusage_linux.c` | `/proc/stat` |
| Android | `cpuusage_linux.c` | Same file; `/proc/stat` is restricted from Android O on |
| GNU/Hurd | `cpuusage_linux.c` | Same file |
| FreeBSD | `cpuusage_bsd.c` | `sysctl kern.cp_times` |
| NetBSD | `cpuusage_bsd.c` | Same file, `sysctl(CTL_KERN, KERN_CP_TIME)` |
| OpenBSD | `cpuusage_bsd.c` | Same file, `KERN_CPTIME` where it exists |
| Solaris / illumos | `cpuusage_sunos.c` | `kstat cpu_stat` per instance |
| Haiku | `cpuusage_haiku.c` | `get_cpu_info()` plus `system_time()` |
| macOS | `cpuusage_apple.c` | `host_processor_info(PROCESSOR_CPU_LOAD_INFO)` |
| Windows | `cpuusage_windows.c` | Perflib `% Processor Utility` on Windows 10+, `NtQuerySystemInformation` below |

All ten platforms have a real implementation — there is no `cpuusage_nosupport.c` in the tree.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `waitTime` | integer | `200` | Sampling window in milliseconds. The schema says `minimum: 1`; the parser accepts `0` and up to `4294967295`. |
| `separate` | boolean | `false` | Print one percentage per logical core instead of the average. |
| `percent` | object | `{ "green": 50, "yellow": 80, "type": 0 }` | Colour thresholds and style for the percentage. `type: 0` means "use `display.percentType`". |
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

`separate` affects the console output only; `--format json` always emits the per-core array.

## Format string

Run `fastfetch -h cpuusage-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{avg}` | Average usage, as a number followed by `%` |
| `{max}` | Highest per-core usage |
| `{max-index}` | Core index of the highest usage |
| `{min}` | Lowest per-core usage |
| `{min-index}` | Core index of the lowest usage |
| `{avg-bar}` | Average usage, as a bar |
| `{max-bar}` | Highest per-core usage, as a bar |
| `{min-bar}` | Lowest per-core usage, as a bar |

`{max-index}` and `{min-index}` are positions in the list the platform returned, which is the same
numbering the platform uses for its cores. They print `999` if no value was usable.

## JSON output

```jsonc
{
    "type": "CPUUsage",
    "result": [
        100.0,
        100.0,
        40.0,
        28.57142857142857,
        25.0,
        19.047619047619047
    ]
}
```

`result` is always the per-core array, with or without `separate`, and always holds raw doubles —
there is no average, no maximum and no index in the object. On failure the object is
`{ "type": "CPUUsage", "error": "…" }`.

## Examples

```jsonc
{ "type": "cpuusage", "waitTime": 500 }
```

```jsonc
{ "type": "cpuusage", "separate": true, "format": "{max} on core {max-index}" }
```

```jsonc
{ "type": "cpuusage", "percent": { "green": 20, "yellow": 60 } }
```

## Pitfalls

- **Two `cpuusage` entries cost twice the `waitTime`.** Each one takes a fresh sample and waits the
  whole window again. Measured on a 10-core machine: one entry with the default settings takes
  0.22 s, two entries take 0.42 s. `--format json` pays the same cost, because the JSON path runs
  the same sampling code.
- **`waitTime: 0` is accepted and then always fails.** The schema declares `minimum: 1`, but the
  parser only rejects negatives and values above `4294967295`, so `0` gets through and the two
  samples land in the same timer tick. The result is the error
  `CPU time did not increase. Try increasing wait time.` instead of a percentage.
- **A failing sample is not a failure exit.** Every error this module can produce is printed in the
  module's own output slot and the process still exits with status `0`. In JSON the same condition
  becomes `{ "type": "CPUUsage", "error": "…" }` with no `result` field.
- **A rejected `waitTime` prints an extra line and keeps the old value.** `waitTime: -1` produces
  the parse error *and* a normal percentage, so one module prints two lines — the first is the
  complaint, the second the measurement, which is still taken with the default 200 ms.
- **`separate` ignores the bar bit.** Per-core output is always a row of numbers; asking for a bar
  adds nothing to it. The colour thresholds still apply.
- **On Windows the JSON array is not capped, the console output is.** `% Processor Utility` can
  exceed 100 % on a boosting CPU, and the module clamps every value to 100 — but only inside the
  console path. `--format json` on Windows can therefore report values above 100 that the default
  output never shows.
- **The retry loop can stretch the window to four times `waitTime`.** If any core's counter did not
  advance between the two samples, the module samples again, up to three more times, each with a
  fresh `waitTime` sleep before giving up. With the default that is up to 800 ms, and the error is
  reported only after the last attempt.
- **`--format json` has no aggregate.** A consumer that wants the average has to compute it, and
  has to decide for itself what to do with the values — the module's own average is the mean of the
  per-core percentages, not a system-wide busy fraction weighted by core count. Those are the same
  number only when every core is reported.
- **`{avg-bar}` and friends are empty by default.** `display.percentType` defaults to
  `num | num-color`, which carries no bar bit, so the three bar variables render as empty strings
  until a bar is requested.
- **Android reports a permission error rather than a number.** `/proc/stat` is not readable from an
  app on Android O and later, and the module reports exactly that instead of falling back to
  another source. On an Android device the module only works under `adb shell` or as root.

## Implementation

`ffPrepareCPUUsage()` runs in the prepare pass of both the JSON-config path and the `--structure`
path. It takes the first sample into a file-scope list and records the timestamp; a second call in
the same run is a no-op. `ffGetCpuUsageResult()` then waits until `waitTime` has elapsed since that
timestamp, takes the second sample, and turns the two into percentages:

```
percent = (inUse2 − inUse1) / (total2 − total1) × 100
```

After a successful sample the first list is overwritten with the second one and the timestamp is
reset, so a second `cpuusage` entry in the same run uses the first entry's result as its baseline.
That is what makes the double-entry case cost a full window again.

The module itself computes the average, the maximum, the minimum and their indices from the list.
Values that are not equal to themselves — the NaN check — are skipped, and the average is divided by
the number of values that survived, so a single unusable core does not drag the average down.

### Linux, Android and GNU/Hurd

`/proc/stat` is read whole and its first line, the `cpu` aggregate, is skipped. Each following
`cpu<N>` line is matched with `sscanf("cpu%*d%llu%llu%llu%llu%llu%llu%llu%*[^\\n]\\n", …)` — user,
nice, system, idle, iowait, irq, softirq. The trailing fields (`steal`, `guest`, `guest_nice`) are
swallowed. Busy time is `user + nice + system + irq + softirq`, and the denominator adds `idle` and
`iowait`. The first line that does not match ends the parse, so a kernel that adds a new column
before the ones read here would silently truncate the list rather than mis-parse it.

### FreeBSD, NetBSD and OpenBSD

`kern.cp_times` is queried once with a null buffer to learn the size, then again into an allocation
of exactly that size. The array is `coreCount × CPUSTATES` 64-bit counters, so the core count falls
straight out of the byte count. Busy time is `CP_USER + CP_NICE + CP_SYS + CP_INTR`, plus `CP_IDLE`
for the denominator. NetBSD and OpenBSD reach the same data through
`sysctl(CTL_KERN, KERN_CPTIME)` — or `KERN_CP_TIME`, whichever the headers define.

### Solaris and illumos

The `cpu_stat` kstat chain is walked by increasing instance id until a lookup or a read fails, which
is how the end of the chain is detected. Busy time is `CPU_USER + CPU_KERNEL`; the denominator adds
`CPU_IDLE` and `CPU_WAIT`. A gap in instance numbering stops the walk early.

### Haiku

`get_system_info()` gives the core count, `get_cpu_info()` gives each core's `active_time`, and
`system_time()` gives the uptime in microseconds. Every core gets the same denominator — the uptime
— so the ratio is that core's utilisation since boot rather than over a window, and the retry check
sees the denominator advance on every call.

### macOS

`host_processor_info(PROCESSOR_CPU_LOAD_INFO)` returns one `processor_cpu_load_info` per core, each
with four counters: user, system, idle and nice. Busy time is user + system + nice, and the
denominator adds idle. The returned array is released with `vm_deallocate()` before returning, and
the reported length is checked against `numCPUs × CPU_STATE_MAX` so a short reply is an error rather
than a partial list.

### Windows

On Windows 10 and later the module first tries the Perflib "Processor Information" counter set,
reading `% Processor Utility` (counter 26) and its base (counter 27) per instance, skipping the
`_Total` instance. Those two are already cumulative numerator and denominator, so the same
difference formula yields a percentage directly — and one that can pass 100 on a boosting CPU,
which is why the console path clamps. The query handle is opened once and kept in a function-scope
static for the life of the process.

If Perflib is unavailable the module falls back to
`NtQuerySystemInformation(SystemProcessorPerformanceInformation)`, sizing the buffer with a first
call that is expected to return `STATUS_INFO_LENGTH_MISMATCH`. `KernelTime` includes idle time, so
idle is subtracted before busy time is formed.
