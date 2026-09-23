# CPUCache

> Print CPU cache sizes

| | |
|---|---|
| Module type | `cpucache` |
| Default order | 34 (only used by `--gen-config`) |
| Module source | `src/modules/cpucache/cpucache.c` |
| Detection source | `src/detection/cpucache/` |

Prints one line per cache level. Each line lists every distinct cache at that level as
`[<count>x]<size> (<type>)`, where the type letter is `I` for instruction, `D` for data, `U` for
unified and `T` for trace. The `Nx` prefix only appears when more than one cache of that shape was
found.

```
CPU Cache (L1): 8x192.00 KiB (I), 8x128.00 KiB (D), 2x128.00 KiB (I), 2x64.00 KiB (D)
CPU Cache (L2): 2x12.00 MiB (U), 4.00 MiB (U)
```

The key is `CPU Cache (L<n>)` — the level lives in the key, not in the value, which is why a custom
`key` has to carry a `{level}` of its own. Sizes are formatted with the global `display.size`
settings, so `display.size.binaryPrefix: "si"` turns the sample above into
`2.88 MB (L1), 28.00 MB (L2)`.

With `compact: true` everything collapses onto a single line, one total per level:

```
CPU Cache: 2.88 MiB (L1), 28.00 MiB (L2)
```

## Platform support

| Platform | Implementation | Data source |
|---|---|---|
| Linux | `cpucache_linux.c` | `/sys/devices/system/cpu/cpuN/cache/indexN/` |
| Android | `cpucache_linux.c` | Same file |
| FreeBSD | `cpucache_shared.c` | SMBIOS type 7, "Cache Information" |
| NetBSD | `cpucache_shared.c` | Same file |
| OpenBSD | `cpucache_shared.c` | Same file |
| Solaris / illumos | `cpucache_shared.c` | Same file |
| Haiku | `cpucache_shared.c` | Same file |
| GNU/Hurd | `cpucache_nosupport.c` | **Not supported** |
| macOS | `cpucache_apple.c` | `sysctl hw.nperflevelN.*` |
| Windows | `cpucache_windows.c` | `NtQuerySystemInformationEx` |

GNU/Hurd is the only platform without an implementation; there the module prints
`Not supported on this platform`. `cpucache_nosupport.c` is referenced exactly once in
`CMakeLists.txt`, from the GNU block.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `compact` | boolean | `false` | Print one line with a total per level instead of one line per level. |
| `key` | string | module name | Module key. A single space hides the key. The key is itself a format string — see below. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

`compact` is the only module-specific key. There are no `show*` filters and no `percent` thresholds —
the level list is whatever the platform reports.

## Format string

Run `fastfetch -h cpucache-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{result}` | The per-level listing (`8x192.00 KiB (I), 8x128.00 KiB (D), …`) |
| `{sum}` | Total size of the caches covered by this line |
| `{level}` | `L1`, `L2`, `L3`, `L4` |

The `key` is a format string too, with a *different* set of variables: `{index}` (the 1-based level
number), `{level}`, `{icon}` and `{module-name}`. `{level}` is the only one both accept.

## JSON output

```jsonc
{
    "type": "CPUCache",
    "result": {
        "l1": [
            { "size": 196608, "num": 8, "lineSize": 128, "type": "instruction" },
            { "size": 131072, "num": 8, "lineSize": 128, "type": "data" }
        ],
        "l2": [
            { "size": 12582912, "num": 2, "lineSize": 128, "type": "unified" },
            { "size": 4194304, "num": 1, "lineSize": 128, "type": "unified" }
        ]
    }
}
```

`size` is in bytes and `lineSize` is in bytes; `type` is one of `instruction`, `data`, `unified`,
`trace` or `unknown`. Levels with no entries are omitted, and `compact` has no effect here. The
array order within a level is insertion order, which is not necessarily instruction-before-data.

## Examples

```jsonc
{ "type": "cpucache", "compact": true }
```

```jsonc
{ "type": "cpucache", "key": "{level}", "format": "{sum}" }
```

```jsonc
{ "type": "cpucache", "format": "{level}: {sum}" }
```

## Pitfalls

- **A gap in the level list truncates everything after it.** Both the text and the JSON path walk
  the four level lists with `result->caches[i].length > 0` as part of the loop condition, so the
  first empty level ends the walk. Measured with a synthetic `L1 + L3` result (L2 empty): the text
  output is a single `L1` line and the JSON is a single `"l1"` array — the 32 MiB L3 entry is
  dropped from both, with no error and exit code 0. Treat this as a signal rather than a feature: a
  contiguous level list is a hardware invariant, so seeing L1 and L3 without L2 means the platform's
  cache description is wrong, and the dropped level is the visible symptom of that. On Linux that
  would be a kernel sysfs defect — fastfetch deliberately does not paper over it.
- **`{level}` is not available in `compact` mode, in either the value or the key.** The compact
  printer passes only `{result}` and `{sum}`, so a format string of `{level}|{sum}` renders as
  `{level}|30.88 MiB` — the placeholder is left verbatim rather than reported. The same happens to
  `{level}` inside `key` when `compact: true`, which is easy to hit because `{level}` in `key` is
  the documented way to label the levels in normal mode.
- **`{sum}` means two different things depending on `compact`.** In normal mode it is the total for
  the line's own level; in compact mode it is the total across *all* levels. Measured on one
  machine: normal mode gives `28.00 MiB` for L2, compact mode gives `30.88 MiB` for the whole set.
- **The L1 sum adds instruction and data caches together.** `{sum}` for L1 is the aggregate of every
  L1 cache in the machine, not a per-core figure, so it looks far larger than any per-core L1 size.
- **On Linux one unreadable cache index kills the whole module.** `parseCpuCacheIndex()` returns an
  error when `level`, `size` or `type` cannot be read, and that error propagates straight out of
  `ffDetectCPUCache()`. Observed in a virtual machine whose `/sys/devices/system/cpu/cpu0/cache/index0/`
  contains `level`, `type`, `shared_cpu_list` and `shared_cpu_map` but **no `size` file at all**: the
  module reports
  `ffReadFileBuffer("/sys/devices/system/cpu/cpuX/cache/indexX/size") == nullptr` and prints no cache
  data whatsoever, even though the other indices were readable.
- **The macOS implementation needs `hw.nperflevels`.** It returns `sysctl(hw.nperflevels) failed`
  when that sysctl is missing or zero, which means the module fails outright rather than falling back
  to a global cache view.
- **On macOS the L2/L3 multiplier is derived, not measured.** The count comes from
  `physicalcpu / cpusperlN` for the performance level, so the total shown is a computed figure. On
  the machine used for the samples above the performance cluster reports a 12 MiB L2 with
  `cpusperl2 = 4` and 8 cores, which is printed as `2x12.00 MiB (U)` — a 24 MiB cluster total. If
  the firmware reports an unexpected `cpusperlN`, the multiplier is wrong in the same proportion.
- **On the SMBIOS platforms `lineSize` is always `0`.** `cpucache_shared.c` passes a literal `0` for
  the line size because SMBIOS type 7 does not carry it, so a consumer that reads `lineSize` gets a
  usable value on Linux, macOS and Windows only.
- **On the SMBIOS platforms the count is a deduplication count.** Each SMBIOS cache record is added
  with `num = 1`, and identical `(type, size, lineSize)` records are merged by incrementing `num`.
  A firmware that describes the same cache once per socket therefore produces a multiplier that has
  nothing to do with sharing.
- **Nothing is printed when no level has entries.** If detection succeeds but every level list is
  empty, the loop body never runs, the module still counts as successful, and the output is empty —
  no key, no value, no error. A consumer keying on the `error` field sees a working module.

## Implementation

`ffPrintCPUCache()` and `ffGenerateCPUCacheJsonResult()` each build a fresh four-element
`FFCPUCacheResult` (one list per level), call `ffDetectCPUCache()` and destroy the lists afterwards.
Nothing is cached, so `--dynamic-interval` re-reads the system every round.

Caches are accumulated through `ffCPUCacheAddItem()` in `src/detection/cpucache/cpucache.h`: it
looks for an existing entry with the same `(type, size, lineSize)` in the level's list and bumps its
`num`, or appends a new one with `num = 1`. Levels outside `1..4` are rejected by the callers.

The text path prints one line per level, building the key from `CPU Cache (L<n>)` when no custom key
is configured, and otherwise running the configured key through the format parser with `{index}`,
`{level}`, `{icon}` and `{module-name}`. The compact path skips the per-level keys entirely and
prints one line with a total per level.

### Linux and Android

`/sys/devices/system/cpu/` is listed for `cpuN` directories; for each one,
`/sys/devices/system/cpu/cpuN/cache/` is listed for `indexN` directories. For each index, `level`
(1–4), `size` (in KiB), `type` (first letter of `Data` / `Instruction` / `Unified` / `Trace`),
`coherency_line_size` and `shared_cpu_list` are read. Deduplication uses the `shared_cpu_list` value
plus the level, size, line size and type, so a cache shared by eight cores is recorded once with
`num = 8` rather than eight times. `coherency_line_size` is optional — its absence leaves the line
size at `0` — while `level`, `size` and `type` are mandatory.

### FreeBSD, NetBSD, OpenBSD, Solaris and Haiku

All five share `cpucache_shared.c`, which walks SMBIOS type 7 records between the first Cache
Information entry and the end-of-table entry. A record is skipped when the "cache enabled" bit in
`CacheConfiguration` is clear or when its installed size is `0`. The level is the low three bits of
`CacheConfiguration` plus one, and the type comes from `SystemCacheType` — `3` maps to instruction,
`4` to data, and everything else to unified. Sizes are read from the 16-bit `InstalledSize` field,
which is in KiB unless bit 15 is set (then 64 KiB units) and is replaced by the 32-bit
`InstalledCacheSize2` field when the 16-bit one is `0xFFFF`. The line size is always reported as
`0`.

### macOS

`hw.nperflevels` gives the number of performance levels and `hw.cachelinesize` gives the one global
line size that is used for every level. For each performance level the code reads
`hw.perflevelN.physicalcpu` and then `l1icachesize`, `l1dcachesize`, `l2cachesize` and
`l3cachesize`; the L1 entries take their `num` directly from the core count, while the L2 and L3
entries divide it by `cpusperl2` / `cpusperl3` to get the number of instances. Levels with no size
reported are skipped, which is why an L4 array never appears on Apple Silicon.

### Windows

`NtQuerySystemInformationEx(SystemLogicalProcessorAndGroupInformation, RelationCache)` returns a
variable-length array of `SYSTEM_LOGICAL_PROCESSOR_INFORMATION_EX` records, walked by their own
`Size` field. Records with `Relationship == RelationCache` and a level between 1 and 4 are added;
`CacheUnified`, `CacheInstruction`, `CacheData` and `CacheTrace` map onto the four types, and any
other type falls through to `0`, which is the unified value. The size and line size come straight
from the record.
