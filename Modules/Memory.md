# Memory

> Print system memory usage information

| | |
|---|---|
| Module type | `memory` |
| Default order | 39 (only used by `--gen-config`) |
| Module source | `src/modules/memory/memory.c` |
| Detection source | `src/detection/memory/` |

Prints how much RAM is in use, how much is installed, and the percentage in use. The key is
`Memory` by default.

```
Memory: 13.58 GiB / 16.00 GiB (85%)
```

The two sizes are formatted with the global `display.size` settings, so a configuration that asks
for SI prefixes prints `14.58 GB / 17.18 GB` instead.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `memory_linux.c` | Parses `/proc/meminfo`, then adjusts for the ZFS ARC |
| Android | `memory_linux.c` | Same file |
| GNU/Hurd | `memory_linux.c` | Same file |
| FreeBSD | `memory_bsd.c` | `sysctl` page counters, then the ZFS ARC |
| NetBSD | `memory_nbsd.c` | `sysctl(CTL_VM, VM_UVMEXP2)`, then the ZFS ARC |
| OpenBSD | `memory_obsd.c` | `sysctl(CTL_VM, VM_UVMEXP)`, no ZFS adjustment |
| Solaris / illumos | `memory_sunos.c` | `sysconf` page counters, then the ZFS ARC |
| Haiku | `memory_haiku.c` | `get_system_info()` |
| macOS | `memory_apple.c` | `sysctl` plus `host_statistics64` |
| Windows | `memory_windows.c` | `NtQuerySystemInformation` |

All ten platforms have a real implementation. There is no `memory_nosupport.c` in the tree and no
platform block references one, so the module never reports "Not supported on this platform".

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `percent` | object | `{ "green": 50, "yellow": 80, "type": 0 }` | Colour thresholds and style for the percentage. `type: 0` means "use `display.percentType"`. |
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

There are no module-specific keys beyond `percent`. `percent` is what colours the number in the
default output and fills `{percentage}`.

## Format string

Run `fastfetch -h memory-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{used}` | Used size, formatted with `display.size` |
| `{total}` | Total size, formatted with `display.size` |
| `{percentage}` | Percentage used, as a number followed by `%` — no parentheses |
| `{percentage-bar}` | Percentage used, as a bar |

`{used}` and `{total}` render a formatted size (`13.58 GiB`), not a byte count. There is no format
variable that yields the raw byte total; use `--format json` for that.

## JSON output

```jsonc
{
    "type": "Memory",
    "result": {
        "total": 17179869184,
        "used": 14574141440
    }
}
```

Both fields are raw bytes. On failure the object is `{ "type": "Memory", "error": "…" }`.

## Examples

```jsonc
{ "type": "memory", "format": "{used} / {total}" }
```

```jsonc
{ "type": "memory", "percent": { "green": 70, "yellow": 90 } }
```

```jsonc
{ "type": "memory", "format": "{percentage-bar} {percentage}" }
```

## Pitfalls

- **`used` is not `total − free`.** Every implementation that can identify reclaimable memory
  subtracts it, so the number is "memory that is actually in use" and is consistently lower than
  what a `free`-based reading gives. On Linux it comes from `MemAvailable`; on FreeBSD, NetBSD and
  Solaris from the page counters plus the reclaimable part of the ZFS ARC. Comparing this number
  against another tool's is comparing two different definitions.
- **The ZFS ARC is subtracted on Linux, FreeBSD, NetBSD and Solaris only.** On those four a machine
  with a large ZFS cache reports a much lower `used` than the same machine would on OpenBSD, macOS,
  Windows or Haiku. The subtraction is `arcstats.size − arcstats.c_min`, guarded so it can never
  push `used` below zero, and it is skipped entirely when the kstat file is unreadable.
- **On macOS `total` is the installed size, not the usable size.** `hw.memsize` counts the memory
  the firmware reserves, so a 16 GiB machine reports `16.00 GiB` while the OS can address slightly
  less. Configure with `-DENABLE_APPLE_MEMSIZE_USABLE=ON` to read `hw.memsize_usable` instead, which
  makes the figure match what other platforms report. The option is off by default.
- **On macOS `used` counts the file cache as free.** It is computed as
  `total − (free + file-backed)`, so a run right after heavy file I/O reports a lower figure than
  Activity Monitor's "Memory Used". The speculative page count is subtracted from the free count
  first, which is the same correction `vm_stat` does.
- **`{percentage-bar}` is empty by default.** `display.percentType` defaults to
  `num | num-color`, which carries neither the bar bit nor the hide-others bit, so the bar variable
  renders as an empty string until you ask for a bar — either through
  `display.percentType` or through `percent.type` in this module's own config.
- **A total of `0` prints the word `Disabled`, and it is not an error.** The default output has a
  special case for it; `--format json` has none, so the same machine reports
  `{ "total": 0, "used": 0 }` with no marker. A consumer that keys on the `error` field will read a
  disabled memory module as a working one.
- **`{percentage}` never prints `nan`.** The division is guarded, so a zero total yields `0%` rather
  than an invalid number.
- **The JSON field order is `total` then `used`**, which is the reverse of both the default output
  and the format-variable list. Harmless for a parser, but it does mean a hand-written
  `## JSON output` sample is easy to get wrong.

## Implementation

`ffPrintMemory()` and `ffGenerateMemoryJsonResult()` both call `ffDetectMemory()` and nothing else —
there is no cache, so under `--dynamic-interval` the module re-reads the system on every round.

### Linux, Android and GNU/Hurd

`/proc/meminfo` is read into a fixed buffer with `ffReadFileData()` and parsed with `memmem()`.
`MemTotal` is mandatory — if it is missing the module reports `MemTotal not found in /proc/meminfo`
— while every other field is optional and defaults to zero.

`MemAvailable` is used for the free part when it is plausible. Because it can be absent (older
kernels) or plainly wrong (reported as greater than the total), the code falls back to
`MemFree + Buffers + Cached + SReclaimable − Shmem` when `MemAvailable` is `0` or at least
`MemTotal`. Both branches end up in `used = total − available`.

The ZFS adjustment then reads `/proc/spl/kstat/zfs/arcstats`, skips its two header lines, finds the
`data` column and picks `size` and `c_min` out of the table. `size − c_min` is subtracted from
`used` when it is positive and smaller than `used`.

### FreeBSD

`hw.physmem` gives the total. The free part is
`v_free_count + v_inactive_count + v_cache_count` shifted by the page-size shift, plus
`vfs.bufspace`, and the ZFS ARC is then handled through `kstat.zfs.misc.arcstats.size` and
`.c_min`, the same way as on Linux.

### NetBSD and OpenBSD

Both read the UVM page counters and compute
`used = (active + inactive + wired) << pageSizeShift`. NetBSD takes the total from
`uvmexp_sysctl.npages` and has the ZFS adjustment; OpenBSD takes it from `uvmexp.npages` and does
not.

### Solaris and illumos

`sysconf(_SC_PHYS_PAGES)` and `sysconf(_SC_AVPHYS_PAGES)` give the two totals. The kstat chain is
then walked for `zfs:0:arcstats`, and `size − c_min` is subtracted from `used` — but only when both
values really are `KSTAT_DATA_UINT64`, so a differently typed kstat is ignored rather than
misread. The kstat handle is closed through a `cleanup` attribute, so an early return cannot leak
it.

### Haiku

A single `get_system_info()` call: `max_pages` for the total, `used_pages` for the used part, both
shifted by the page-size shift.

### macOS

The total comes from `sysctl(CTL_HW, HW_MEMSIZE)`, or from `hw.memsize_usable` in a build configured
with `-DENABLE_APPLE_MEMSIZE_USABLE=ON`. The used part is derived from
`host_statistics64(HOST_VM_INFO64)`: the free page count has the speculative count removed, the
external (file-backed) page count is added back, and the sum is shifted and subtracted from the
total.

### Windows

`NtQuerySystemInformation(SystemBasicPerformanceInformation)` supplies `AvailablePages`. The
physical page count is read straight out of `SharedUserData`, which needs no call at all; on 64-bit
builds `FullNumberOfPhysicalPages` is preferred and `NumberOfPhysicalPages` — a `ULONG` capped at
16 TB — is the fallback for Windows 8.1, where the wide field is not populated.
