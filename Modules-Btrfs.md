# Btrfs

> Print Linux BTRFS volumes

| | |
|---|---|
| Module type | `btrfs` |
| Default order | 43 (only used by `--gen-config`) |
| Module source | `src/modules/btrfs/btrfs.c` |
| Detection source | `src/detection/btrfs/` |

Prints one line per BTRFS volume, discovered as the UUID directories under `/sys/fs/btrfs/`. The key
is `BTRFS (<label>)`, or plain `BTRFS` for an unlabelled volume; note that the printed key is
upper-case while the module type is `btrfs`.

```
BTRFS (root): 146.03 GiB / 465.76 GiB (31%, 40% allocated)
```

The three figures are `used / total`, then the used percentage and the allocated percentage. `used`
and `allocated` are sums over the three allocation groups (data, metadata, system), each multiplied
by its profile's copy count; `total` is the sum of the sizes of the member devices. The sizes go
through `display.size` and the percentages through `display.percentType` and this module's `percent`
object.

The default `percent` is `{ "green": 50, "yellow": 80 }` — the normal interpretation, so up to 50 %
is green, 50–80 % yellow and above that red.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `btrfs_linux.c` | Walks `/sys/fs/btrfs/` |
| Android | `btrfs_nosupport.c` | Reports "Not supported on this platform" |
| FreeBSD / MidnightBSD / DragonFly | `btrfs_nosupport.c` | Not supported |
| NetBSD | `btrfs_nosupport.c` | Not supported |
| OpenBSD | `btrfs_nosupport.c` | Not supported |
| Solaris / illumos | `btrfs_nosupport.c` | Not supported |
| Haiku | `btrfs_nosupport.c` | Not supported |
| GNU/Hurd | `btrfs_nosupport.c` | Not supported |
| macOS | `btrfs_nosupport.c` | Not supported |
| Windows | `btrfs_nosupport.c` | Not supported |

Linux is the only platform with an implementation, and even there the module needs the `btrfs`
kernel module loaded — without it `/sys/fs/btrfs` does not exist and the module reports an error
instead of an empty list.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `percent` | object | `{ "green": 50, "yellow": 80, "type": 0 }` | Colour thresholds and style for the two percentages. `type: 0` inherits `display.percentType`. |
| `key` | string | `BTRFS (<label>)` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

## Format string

Run `fastfetch -h btrfs-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{name}` | Volume label — also available in key format |
| `{uuid}` | Volume UUID, which is also the `/sys/fs/btrfs/<uuid>` directory name |
| `{devices}` | Comma-separated member device names |
| `{features}` | Comma-separated enabled feature names |
| `{used}` | Size in use, formatted with `display.size` |
| `{allocated}` | Size allocated, formatted with `display.size` |
| `{total}` | Total size, formatted with `display.size` |
| `{used-percentage}` | Used percentage as a number; empty unless the style includes the number |
| `{allocated-percentage}` | Allocated percentage as a number; empty unless the style includes the number |
| `{used-percentage-bar}` | Used percentage as a bar, brackets included; empty unless the style includes a bar |
| `{allocated-percentage-bar}` | Allocated percentage as a bar; empty unless the style includes a bar |
| `{node-size}` | Metadata node size, formatted with `display.size` |
| `{sector-size}` | Sector size, formatted with `display.size` |

`{name}` is the only variable that is available in the key format. The three size variables are
already-formatted strings, so they honour `display.size.binaryPrefix` — unlike the JSON, which
carries raw bytes.

## JSON output

```jsonc
{
    "type": "Btrfs",
    "result": [
        {
            "name": "root",
            "uuid": "00000000-0000-0000-0000-000000000000",
            "devices": "nvme0n1p2",
            "features": "big_metadata,compress_lzo,extended_iref,mixed_backref,skinny_metadata",
            "generation": 123456,
            "nodeSize": 16384,
            "sectorSize": 4096,
            "totalSize": 500107862016,
            "globalReservation": { "used": 12345678, "total": 16777216 },
            "allocation": [
                { "type": "data",     "profile": "single", "copies": 1, "used": 104857600, "total": 134217728 },
                { "type": "metadata", "profile": "dup",    "copies": 2, "used": 8388608,   "total": 16777216 },
                { "type": "system",   "profile": "dup",    "copies": 2, "used": 16384,     "total": 8388608 }
            ]
        }
    ]
}
```

`allocation` always has exactly three entries, in the order `data`, `metadata`, `system`. All sizes
are raw bytes, `totalSize` is the sum of the member devices, and `generation` is the file system
generation counter. On failure the object is `{ "type": "Btrfs", "error": "…" }`.

`globalReservation` describes the kernel's global block reserve, the pool that metadata operations
fall back on when ordinary space is exhausted. `total` is the size of the reserve
(`allocation/global_rsv_size`, which the kernel reads from `btrfs_block_rsv.size`) and `used` is the
part of it that is currently taken (`allocation/global_rsv_reserved`, from `btrfs_block_rsv.reserved`).
Both are raw bytes, and neither feeds the percentages above — those come from `allocation` only.
`btrfs_linux.c` is the only backend that fills the pair in, and the object is always present in a
Linux result, both values `0` in the unlikely case that the kernel does not report the files.

## Examples

```jsonc
// The default output, rebuilt by hand
{ "type": "btrfs", "format": "{used} / {total} ({used-percentage}, {allocated-percentage} allocated)" }
```

```jsonc
// A bar instead of the percentages, one line per volume
{ "type": "btrfs", "percent": { "type": ["num", "bar"] }, "format": "{used-percentage-bar} {used} / {total}" }
```

```jsonc
// Show what the volume is made of
{ "type": "btrfs", "key": "{name}", "format": "{devices} [{features}] {node-size}/{sector-size}" }
```

## Pitfalls

- **`total` is the raw capacity of the member devices, not the usable size.** The detector adds up
  every device's `size` file, so a two-device RAID1 volume reports twice the capacity that is
  actually available, and RAID5/RAID6 volumes are not reduced by the parity overhead (both profiles
  are counted with `copies = 1`, with `(n-1)/n` and `(n-2)/n` left as comments only). The
  percentages are therefore a share of raw device capacity and will not match
  `btrfs filesystem usage` or `df`.
- **`used` and `allocated` are multiplied by the profile's copy count** — `dup`, `raid1` and
  `raid10` ×2, `raid1c3` ×3, `raid1c4` ×4, and `single`, `raid0`, `raid5`, `raid6` ×1. A `dup`
  metadata profile therefore reports twice the bytes that were written, on a single-device volume.
- **`generation` reaches the JSON but has no format variable**, and the two global-reservation
  values the detector reads (`allocation/global_rsv_size` and `allocation/global_rsv_reserved`) are
  exposed nowhere — neither as a variable nor in the JSON result.
- **A machine without BTRFS gets an error, not an empty list.** `/sys/fs/btrfs` only exists when the
  kernel module is loaded, so a distribution that does not mount any btrfs volume reports
  `opendir("/sys/fs/btrfs") == nullptr`, and the non-Linux backends report
  `Not supported on this platform`. Both go through `ffPrintError()`, which is silent unless
  `display.showErrors` is on — but `--format json` always writes the message into `error`, so a JSON
  run is the only way to see what happened on a quiet text run.
- **With more than one volume the printer appends the volume number to the key**, because the module
  hands the real index to `ffPrintLogoAndKey()`. Two volumes labelled `root` and `data` print
  `BTRFS (root) 1` and `BTRFS (data) 2`; the same number also appears in `{index}` inside a custom
  `key`, so a key such as `"{index} {name}"` renders `1 root 1`. Modules that build their own key
  (`disk`, `physicaldisk`, `bluetoothradio`) pass `0` here and have no suffix.
- **A volume whose `/sys/fs/btrfs/<uuid>` directory cannot be opened is dropped silently.** The
  detector `continue`s past it, so it is missing from both the text output and the JSON rather than
  reported as an error.
- **Only 36-character directory names are treated as volumes.** Anything else under `/sys/fs/btrfs`
  is skipped, which is what filters out the non-UUID entries the kernel keeps there.
- **An unlabelled volume prints the bare key `BTRFS`.** The label is read from the `label` file with
  trailing whitespace trimmed, and an empty result falls back to the module name — so on a machine
  with several unlabelled volumes the trailing number is the only thing telling the lines apart.

## Implementation

`ffDetectBtrfs()` fills a list of `FFBtrfsResult` and returns an error string; `ffPrintBtrfs()` walks
the list, and `ffGenerateBtrfsJsonResult()` serialises it. Both entry points call the detector
themselves. Nothing is cached, so a `--dynamic-interval` run re-reads sysfs on every round.

`printBtrfs()` builds the key, converts the three sizes with `ffSizeAppendNum()` — which is what
makes them respect `display.size` — and computes both percentages as `used / totalSize × 100`, with
`0` when `totalSize` is `0`. The default line then prints `<used> / <total> (` + used% + `, ` +
allocated% + ` allocated)`.

### Linux

`/sys/fs/btrfs/` is walked with `opendir()`; an entry is kept only when its name is exactly the
36 characters of a UUID string, and the per-UUID directory is then opened with `openat()` and kept
as a descriptor, so the rest of the reads are relative to it. Per volume:

- `label` → `name` (trailing whitespace trimmed).
- `devices/` → the member device names, comma-separated; each `<device>/size` is read and multiplied
  by 512 to get that device's byte size, and the results are summed into `totalSize`.
- `features/` → the feature names, comma-separated.
- `generation`, `nodesize`, `sectorsize` → the three scalar fields.
- `allocation/` → `global_rsv_size` (required; anything else aborts the volume with an error string
  that the caller ignores) and `global_rsv_reserved`, from which `globalReservationUsed` is computed
  as `total - reserved`. For each of the three groups the detector reads `<type>/total_bytes` and
  `<type>/bytes_used` and then probes `<type>/<profile>/` with `faccessat()` in the fixed order
  `single`, `dup`, `raid0`, `raid1`, `raid10`, `raid1c3`, `raid1c4`, `raid5`, `raid6`, storing the
  matching profile and its copy count (`1, 2, 1, 2, 2, 3, 4, 1, 1`), or `unknown` with `copies = 1`
  when none of them exists.

The error strings of the three per-volume helpers (`enumerateDevices()`, `enumerateFeatures()`,
`detectAllocation()`) are discarded by the caller, so a partially read volume is indistinguishable
from a complete one; the only errors that reach `ffPrintError()` are the ones `ffDetectBtrfs()`
itself returns.
