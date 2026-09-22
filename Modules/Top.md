# Top

> Print processes with the highest CPU, memory or disk I/O usage

| | |
|---|---|
| Module type | `top` |
| Default order | 37 (only used by `--gen-config`) |
| Module source | `src/modules/top/top.c` |
| Detection source | `src/detection/top/` |

Prints one line per process, ranked by the metric chosen with `sort`. The key is `Top Processes`,
numbered from 1 when more than one process is printed.

```
Top Processes 1: firefox (1234) - CPU 42% - MEM 1.21 GiB - DSK 245.76 KiB/s / 0 B/s - THR 63
Top Processes 2: code (5678) - CPU 12% - MEM 484.29 MiB - DSK 731.34 KiB/s / 13.72 KiB/s - THR 36
```

`DSK` is `<read>/s / <write>/s`, `MEM` is the resident set size and `THR` is the thread count. A
single result loses the index, and `compact: true` collapses everything into one line of names:

```
Top Processes: firefox code gtkd gdbus
```

Unlike `Modules/Processes`, this module needs **two** readings of the process table to turn CPU time
and I/O counters into rates, so it waits before printing. See `waitTime` below and the note in
`Modules/DiskIO`, which uses the same sampling scheme.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `top_linux.c` | `/proc/<pid>/stat`, plus `statm` and `io` when requested |
| Android | `top_linux.c` | Same reader, subject to `/proc` restrictions |
| FreeBSD | `top_bsd.c` | `sysctl({CTL_KERN, KERN_PROC, KERN_PROC_PROC})` |
| DragonFly | `top_dbsd.c` | `KERN_PROC_ALL`, skipping zombies |
| NetBSD | `top_nbsd.c` | `KERN_PROC2`, plus one `KERN_LWP` call per process for the thread count |
| OpenBSD | `top_obsd.c` | `KERN_PROC_ALL`, plus a second pass with `KERN_PROC_SHOW_THREADS` |
| Solaris / illumos | `top_sunos.c` | `/proc/<pid>/status`, `psinfo` and (for disk) `usage` |
| Haiku | `top_haiku.c` | `get_next_team_info()` plus `get_next_area_info()` per team |
| GNU/Hurd | `top_gnu.c` | `libps`, with `PSTAT_TASK_BASIC` requested |
| macOS | `top_apple.c` | `proc_listallpids()`, then `proc_pidinfo()` and `proc_pid_rusage()` per process |
| Windows | `top_windows.c` | `NtQuerySystemInformation(SystemProcessInformation)` |

Every platform has a real implementation; there is no `top_nosupport.c`.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `sort` | string | `"cpu"` | Ranking metric: `cpu`, `memory`, `disk-read`, `disk-write`, `start-time` or `threads` |
| `showTypes` | string or array | `["cpu","memory","disk"]` | What is collected and printed: `cpu`, `memory`, `disk`, `threads` |
| `processes` | integer | `5` | Number of processes to print |
| `waitTime` | integer | `250` | Milliseconds between the two process-table readings |
| `compact` | boolean | `false` | Print every process name on a single line |
| `percent` | object | `green: 50`, `yellow: 80` | Colour thresholds for the CPU percentage |
| `key` | string | `Top Processes` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

`sort` and `showTypes` are **not** the same option with two spellings, and their value sets differ:
`sort` additionally accepts `disk-read`, `disk-write` and `start-time`, while `showTypes` accepts
`disk` (which stands for both disk directions) and rejects all three of those. A value that is valid
for one is therefore rejected by the other.

On GNU/Hurd and Haiku the default `showTypes` is `["cpu","memory"]`: neither platform exposes
per-process storage I/O counters, so `disk` is left out of the default rather than collected as
zeros. Both platforms still accept `disk` if it is asked for explicitly.

`showTypes` also accepts a bare string (`"showTypes": "threads"`), which sets a single value instead
of a list. With `cpu` and `disk` both absent — that is, a memory-only or threads-only configuration —
the sampling wait is skipped entirely and a single reading of the process table is used.

`percent.type` is the standard percentage bit mask; `0` (the default here) means "use
`display.percent.type`". The thresholds are the usual pair, and with `green <= yellow` the meaning is
`[0, green]` green, `(green, yellow]` yellow, `(yellow, 100]` red.

## Format string

Run `fastfetch -h top-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{name}` | Process name |
| `{pid}` | Process ID |
| `{cpu}` | CPU usage as a raw number |
| `{mem}` | Resident memory in bytes |
| `{disk-read}` | Read bytes per second |
| `{disk-write}` | Written bytes per second |
| `{threads}` | Thread count |
| `{cpu-percentage}` | CPU usage as a formatted percentage |
| `{mem-formatted}` | Resident memory, formatted |
| `{disk-read-formatted}` | Read rate, formatted, with a `/s` suffix |
| `{disk-write-formatted}` | Write rate, formatted, with a `/s` suffix |

No variable carries the `*` marker, so **none of them is available in the key format**: `key:
"T[{name}]"` prints the literal text `T[{name}]` for every line.

The default output is built from `{name} ({pid})` plus one `- CPU …` / `- MEM …` / `- DSK …` /
`- THR …` field per entry of `showTypes`, so a custom `format` can reproduce it, with the caveat that
the CPU field is the *formatted* value and not the raw one.

## JSON output

```jsonc
{
    "type": "Top",
    "result": [
        {
            "name": "firefox",
            "pid": 1234,
            "cpuPercent": 249.64076085663166,
            "mem": 7979405312,
            "bytesRead": 238007,
            "bytesWritten": 0,
            "threads": 277,
            "startTime": 134345504538919309
        }
    ]
}
```

`cpuPercent`, `mem`, `bytesRead`/`bytesWritten` and `threads` are present only when the corresponding
entry is in `showTypes` — `bytesRead` and `bytesWritten` share the single `disk` entry. `startTime` is
always written, whether or not it is asked for. On failure the object is `{ "type": "Top", "error": "…" }`,
and `"processes": 0` produces `"result": []`.

The JSON result does not know about `compact`, so a compacted text run and a JSON run of the same
configuration return the same per-process data.

## Examples

```jsonc
// Rank by resident memory, printing only memory
{ "type": "top", "sort": "memory", "processes": 3, "showTypes": ["memory"] }
```

```jsonc
// Rank by thread count and print only that
{ "type": "top", "sort": "threads", "showTypes": ["threads"], "format": "{name}: {threads} threads" }
```

```jsonc
// One line, ten names
{ "type": "top", "compact": true, "processes": 10 }
```

```jsonc
// Sample over a full second instead of the default 250 ms
{ "type": "top", "showTypes": ["cpu", "memory"], "waitTime": 1000 }
```

## Pitfalls

- **The wait is a floor, not a delay.** The first reading is taken while the configuration is being
  parsed, before any module prints, and the detector only sleeps if less than `waitTime` has elapsed
  since. Everything that runs in between therefore pushes the second reading later, so the real
  sampling window is "at least `waitTime`". `waitTime: 0` removes the wait completely, which is why
  the two readings can land in the same clock tick and every rate comes out as `0`.
- **A second `top` module shares the first one's baseline.** The pair of readings lives in file-scope
  statics and the prepare step runs only once per process, so two `top` modules in one configuration
  start from the same first reading and each takes its own second one.
- **`sort: "start-time"` is newest-first.** The comparison is descending on the start timestamp, so
  the first line is the most recently created process, not the oldest. Ties are broken by ascending
  pid on every sort key.
- **A wrong value in `sort` or `showTypes` falls back to the default instead of stopping the
  module.** The value is reported as `Invalid <key> value: Invalid enum string` and the option keeps
  its default — `sort: "cpu"`, or the full `["cpu","memory","disk"]` for `showTypes`. Since
  `display.showErrors` defaults to `false`, the usual symptom is a silently wrong ranking.
- **`sort: "threads"` needs `"threads"` in `showTypes` on NetBSD, OpenBSD and GNU/Hurd.** Those three
  fetch the thread count in a separate pass that only runs when `threads` is requested; sorting by a
  count that was never collected compares zeros and the list degrades to ascending pid order. On
  Linux, Windows, macOS, FreeBSD and DragonFly the count comes with the process table for free.
- **`compact: true` silently discards `format`, `percent` and `showTypes`.** The compact branch prints
  nothing but the process names, so a `format` string is never evaluated, the percentage colours are
  never used and the per-field selection has no effect on the line. The key is also printed once,
  without an index.
- **`processes: 0` prints nothing and still counts as success.** The detector returns an empty list
  immediately, so the module emits neither a key nor an error line, and `--format json` answers with
  `"result": []`. The JSON schema declares `"minimum": 1` for this key, so a schema-aware editor
  rejects a value the runtime happily accepts.
- **`startTime` has a different unit on every platform.** It is always present in the JSON result, and
  the value is not comparable across platforms: Windows reports 100 ns `FILETIME` ticks, Linux clock
  ticks since boot, FreeBSD/NetBSD/OpenBSD/Solaris/macOS epoch milliseconds, and Haiku microseconds
  since boot. It is not exposed as a format variable.
- **Zero is the normal value for a metric that was not collected.** `{disk-read}` and `{disk-write}`
  are `0` when `disk` is not in `showTypes`, and on Haiku and GNU/Hurd they are `0` even when it is.
  The CPU figures follow the same rule in the memory-only and threads-only paths, where no sampling
  window exists to divide by.
- **`{cpu-percentage}` can be empty while `{cpu}` still holds the value.** The formatted variable is
  only filled when the percentage type includes the `num` bit, while the raw one is always passed. A
  configuration with `display.percent.type` set to an empty array drops the `CPU` field from the
  default output for the same reason, but leaves `{cpu}` usable in a custom format.
- **The CPU percentage is not clamped to 100.** It is the process's CPU time divided by the wall-clock
  window, summed over every core it used, so a multi-threaded process on a many-core machine reports
  several hundred percent. The colour thresholds compare against that number, which makes the default
  `green: 50` / `yellow: 80` colour almost any busy multi-threaded process red.
- **`{cpu}` and `{cpu-percentage}` are the same number in two shapes.** `{cpu}` goes through the
  generic double formatter (`display.fraction.ndigits`, 2 decimals by default) and `{cpu-percentage}`
  through the percentage formatter, which adds the `%` sign and the colour. The default output uses
  the latter, which is why it prints whole percentages.
- **macOS never lists system processes.** `top_apple.c` skips every entry carrying `PROC_FLAG_SYSTEM`,
  and there is no option to include them.
- **`{mem}` is resident memory, measured differently per platform.** Linux reads the second field of
  `statm` and shifts it by the page size, Windows uses `WorkingSetSize`, Haiku sums `ram_size` over the
  team's areas, Solaris multiplies `pr_rssize` by 1024, and the BSDs shift `ki_rssize`/`p_vm_rssize`.
  The meaning is the same, the exact number is not.
- **Process names come from whatever the platform calls a name.** Linux uses `comm` (15 bytes), the
  BSDs `ki_comm`/`kp_comm`/`p_comm`, Windows the executable's base name, and GNU/Hurd the first
  command-line argument, because the Hurd has no kernel-side name. A long name is truncated on Linux
  and a Hurd process with no arguments is printed as `(unknown)`.
- **Both parse passes report the same warning.** The module object is parsed once while options are
  built and once while printing, so one unknown key produces two identical lines. This is the same
  doubling described in `Modules/NetIO`.

## Implementation

`src/detection/top/top.c` holds the platform-independent half. `ffPrepareTopProcesses()` stores the
first snapshot in a file-scope static together with the tick it was taken at, and
`ffDetectTopProcesses()` takes the second one after sleeping until `waitTime` has elapsed, matches the
two lists by pid, drops any process whose start time changed or whose counters went backwards, and
turns the differences into per-second rates. The result list is then sorted with a per-key comparator
and truncated to `processes`. `ffPrepareTopProcesses()` is called from the option parsers
(`commandoption.c`, `jsonconfig.c`) rather than from the module, which is what makes the wait start
before anything is printed.

Every backend implements `ffTopGetProcessSnapshot()` and fills `FFTopProcessSnapshot`: name, pid,
`cpuTime` in milliseconds, `memBytes`, `bytesRead`/`bytesWritten` as byte counters, `startTime` and
`threads`. The shared code subtracts the two snapshots; a backend that cannot supply a counter leaves
it at zero, and the rate for it then comes out as zero as well.

### Linux and Android

`/proc` is walked through directory descriptors, and each numeric entry is read as
`/proc/<pid>/stat` (1 KiB), `/proc/<pid>/statm` when memory is wanted and `/proc/<pid>/io` when disk
is. The name is taken between the first `(` and the last `)`, so a name containing spaces or
parentheses does not shift the fields, and the remaining fields are walked positionally from field 3
to field 22: field 9 is the flag word, where `PF_KTHREAD` (redefined locally) skips kernel threads,
fields 14 and 15 are user and system time, field 20 is the thread count and field 22 is the start
time. CPU time is converted from clock ticks with `sysconf(_SC_CLK_TCK)`; memory is the second field
of `statm` shifted by `pageSizeShift`; disk counters are the `read_bytes` and `write_bytes` lines of
`/proc/<pid>/io`, which are the physical-storage counters rather than the character counts.

### FreeBSD and DragonFly

Two files, because the CMake FreeBSD block splits on `DragonFly`. FreeBSD uses `KERN_PROC_PROC` and
skips `P_KPROC` entries; DragonFly uses `KERN_PROC_ALL` and skips zombies (`SZOMB`). Both derive the
CPU time from `ru_utime`/`ru_stime` and the block counters from `ru_inblock`/`ru_oublock` scaled by
`DEV_BSIZE`. DragonFly adds the live-lightweight-process ticks from `kp_lwp.kl_uticks`/`kl_sticks`
and the lwp's own block counters, because `kp_ru` only accounts for exited lwps.

### NetBSD and OpenBSD

NetBSD asks for `KERN_PROC2`, skips `P_SYSTEM` entries and processes whose `p_uvalid` is clear, and
fetches the thread count with a second `KERN_LWP` sysctl per process. OpenBSD takes `KERN_PROC_ALL`,
skips entries without `p_uvalid`, and then — only when `threads` was requested — repeats the request
with `KERN_PROC_SHOW_THREADS` and counts, for every process, the entries whose `p_tid` is not `-1`.
That second pass is a nested loop over all processes and all threads. Both platforms scale the block
counters by `DEV_BSIZE`.

### Solaris and illumos

`/proc` is walked like on Linux, but the data comes from `pstatus` (`pr_flags` is tested against
`PR_ISSYS`), `psinfo` (`pr_time`, `pr_rssize`, `pr_start`, `pr_nlwp`, and the `pr_sname == 'Z'` zombie
test) and, for the disk counters, `usage` (`pr_inblk`/`pr_oublk` scaled by `DEV_BSIZE`). The data
model of the returned structures depends on the caller, not on the observed process, which is why the
same `psinfo_t` works for both 32-bit and 64-bit processes.

### macOS

`proc_listallpids()` is called twice: the first call returns a pid count, the second wants a *byte*
size, so the buffer is sized as `count + count/8 + 1` entries. Each pid is then read with
`proc_pidinfo(PROC_PIDTASKALLINFO)`, which supplies the name (`pbi_name`, falling back to `pbi_comm`),
the CPU time, the resident size, the start time and the thread count in one call. The disk counters
come from a separate `proc_pid_rusage(RUSAGE_INFO_V2)` call, made only when disk was requested.
Entries with `PROC_FLAG_SYSTEM` are skipped.

### Haiku and GNU/Hurd

Haiku enumerates teams with `get_next_team_info()`, skips `B_SYSTEM_TEAM`, and sums `ram_size` over
all areas of the team for the memory figure; the CPU time comes from
`get_team_usage_info(B_TEAM_USAGE_SELF)`. `start_time` is microseconds since boot, and the storage I/O
counters are not exposed by the OS at all.

GNU/Hurd builds a `proc_stat_list` through `libps` with `PSTAT_ARGS | PSTAT_STATE |
PSTAT_TASK_BASIC`, plus `PSTAT_NUM_THREADS` when threads were requested, and checks
`proc_stat_has()` before reading each field — a flag that could not be fetched is simply left unset.
Pid 2 is the kernel task and is skipped; the name is the process's first argument.

### Windows

`NtQuerySystemInformation(SystemProcessInformation)` is called with a growing buffer, retrying up to
four times on `STATUS_INFO_LENGTH_MISMATCH`, and the result is walked as a linked list through
`NextEntryOffset`. Entries with a parent pid of 0 are skipped. The CPU time is the sum of `UserTime`
and `KernelTime` converted from 100 ns units to milliseconds, memory is `WorkingSetSize`, the disk
counters are `IoCounters.ReadTransferCount`/`WriteTransferCount`, and `startTime` is left in raw
`FILETIME` ticks.
