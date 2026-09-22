# DiskIO

> Print physical disk I/O throughput

| | |
|---|---|
| Module type | `diskio` |
| Default order | 68 (only used by `--gen-config`) |
| Module source | `src/modules/diskio/diskio.c` |
| Detection source | `src/detection/diskio/` |

Prints one line per physical device with the data read and written since the previous snapshot,
divided by the elapsed time — a rate in bytes per second. The key is `Disk I/O (<device name>)`,
falling back to the device path when the name is empty.

```
Disk I/O (NVMe SSD 1TB): 990.04 KiB/s (R) - 0 B/s (W)
Disk I/O (USB Flash Disk): 0 B/s (R) - 12.50 KiB/s (W)
```

The `(R)` and `(W)` markers mean read and write. With `detectTotal: true` the module prints the
cumulative counters since boot instead of a rate, and the `/s` suffix disappears.

Because a rate needs two readings, this module **waits** `waitTime` milliseconds between them, so a
run with the default configuration takes about a quarter of a second longer than a run without it.
`detectTotal: true` needs only one reading and does not wait.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `diskio_linux.c` | `/sys/block/<dev>/stat`, in 512-byte sectors |
| Android | `diskio_linux.c` | Same file |
| GNU/Hurd | `diskio_nosupport.c` | Reports "Not supported on this platform" |
| FreeBSD | `diskio_bsd.c` | libgeom providers plus a devstat snapshot |
| NetBSD | `diskio_nbsd.c` | `sysctl(HW_IOSTATS)` |
| OpenBSD | `diskio_obsd.c` | `sysctl(HW_DISKSTATS)` |
| Solaris / illumos | `diskio_sunos.c` | kstat chain, class `disk` |
| Haiku | `diskio_nosupport.c` | Reports "Not supported on this platform" |
| macOS | `diskio_apple.c` | IOKit `IOBlockStorageDriver` statistics |
| Windows | `diskio_windows.c` | `\\.\PhysicalDriveN` and `\\.\CDROMN` |

Haiku and GNU/Hurd have no implementation at all, so the module is registered but always fails
there.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `namePrefix` | string | empty | Only show devices whose name starts with this string |
| `detectTotal` | boolean | `false` | Print cumulative counters instead of a per-second rate |
| `waitTime` | integer | `250` | Milliseconds between the two counter readings. Ignored when `detectTotal` is true. |
| `key` | string | `Disk I/O (<name>)` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

`waitTime` accepts `0`…`4294967295`, but `0` is silently raised to `1`. Anything outside the range —
including a negative number — is rejected with a message instead of being reinterpreted, and the
module prints that message in place of its output.

## Format string

Run `fastfetch -h diskio-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{name}` | Device name — also available in key format |
| `{dev-path}` | Device path — also available in key format |
| `{size-read}` | Bytes read, formatted with `display.size`, plus `/s` unless `detectTotal` |
| `{size-written}` | Bytes written, formatted the same way |
| `{bytes-read}` | Bytes read per second, as a raw number |
| `{bytes-written}` | Bytes written per second, as a raw number |
| `{read-count}` | Read operations per second |
| `{write-count}` | Write operations per second |

Every `…-count` and `…-bytes` value is already a rate, so multiplying is never necessary — the
description's "[per second]" is carried by the value, not by a suffix. Only `{size-read}` and
`{size-written}` bring the `/s` suffix themselves.

## JSON output

```jsonc
{
    "type": "DiskIO",
    "result": [
        {
            "name": "NVMe SSD 1TB",
            "devPath": "/dev/nvme0n1",
            "bytesRead": 1013786,
            "bytesWritten": 0,
            "readCount": 27,
            "writeCount": 0
        }
    ]
}
```

The four numeric fields are per-second rates unless `detectTotal` is set, in which case they are the
raw cumulative counters. On failure the object is `{ "type": "DiskIO", "error": "…" }`.

## Examples

```jsonc
// Cumulative counters, no waiting
{ "type": "diskio", "detectTotal": true }
```

```jsonc
// A longer measurement window for a quieter machine
{ "type": "diskio", "waitTime": 1000 }
```

```jsonc
// Compact read/write line for a single device
{ "type": "diskio", "namePrefix": "NVMe", "format": "R {size-read}  W {size-written}" }
```

```jsonc
// Packet-style counters, raw numbers
{ "type": "diskio", "format": "{read-count} reads, {write-count} writes, {bytes-read} B/s" }
```

## Pitfalls

- **The module blocks for `waitTime` milliseconds.** The default 250 ms is paid by every run that
  uses the rate path, including `--format json`. `detectTotal: true` skips the wait — which is the
  reason to prefer it in scripts that only want a total.
- **A large `waitTime` really does block.** The value is capped at `4294967295` ms — about 49.7 days
  — with no warning, so a typo like `250000` (four minutes) or a garbage value is only noticed as a
  fastfetch that never returns. `0` is quietly rewritten to `1`, so it is not a way to disable the
  wait.
- **`namePrefix` is matched against the vendor/model string, not the device node.** On Linux the
  name is `vendor` plus `model` from `/sys`, so a virtio disk is literally called `0x1af4` and
  `"namePrefix": "vd"` matches nothing. Use `{dev-path}` in a `format` — or `--format json` — to
  filter by device node.
- **A `namePrefix` that matches nothing is silent with `detectTotal: true`.** The rate path reports
  `No physical disk found`, but only when `display.showErrors` is on; the total path returns an empty
  list and the module prints nothing at all, with exit status 0 in both cases.
- **The counters come from the disk, not from the file system.** Page-cache hits never reach them, so
  a `cat` of a cached file shows up as no I/O at all; the numbers reflect what the device was asked
  to do. This is the same counter `iostat` reads.
- **On macOS a mounted disk image counts as a physical disk.** The detector walks
  `IOBlockStorageDriver`, which includes the virtual media behind `.dmg` images, so a line for
  `Apple disk image Media` can appear next to the real SSD.
- **On Solaris `{dev-path}` is always empty.** There is no `/dev/<name>` for a kstat disk there, and
  the module fills the field with an empty string rather than guessing a path; the default key falls
  back to the device name, which is never empty on that platform.
- **Two snapshots can disagree.** A USB disk plugged in between them, or a device path that changed,
  aborts the module with `Different number of physical disks. Hardware change?` or
  `Physical disk device path changed`. Like every module error, it is invisible unless
  `display.showErrors` is true.
- **The JSON array is not sorted, the text output is.** The sort by name happens while printing, so
  `--format json` returns the detection order — `readdir()` order for Linux, the sysctl/IOKit order
  elsewhere.
- **Under `--dynamic-interval` the elapsed time is the round interval, not `waitTime`.** The baseline
  is kept in a file-scope static and deliberately not dropped by the cache layer, so instead of
  taking two readings per round the module takes the previous round's second reading as its new
  baseline. Each round therefore reports the average rate over the whole round, and `waitTime` only
  sets the minimum spacing between rounds.
- **Every option warning is printed twice when the module is configured from a file.** The module
  object is parsed once in the prepare pass and once while printing, and the parser reports during
  both: a config with one unknown key and one out-of-range `waitTime` produces four lines, not two.
  The same doubling happens with `-s diskio` as long as a config supplies the options.

## Implementation

All the backends implement `ffDiskIOGetIoCounters()` and nothing else; the rate arithmetic is shared.
`ffPrepareDiskIO()` takes the first snapshot and stores it in a file-scope static together with the
time it was taken, and `ffDetectDiskIO()` takes the second one after sleeping until `waitTime` has
elapsed, then replaces each counter with `(current − previous) × 1000 / elapsed_ms` while leaving the
raw value behind as the next baseline. The counters that are compared are contiguous
`uint64_t` fields, so one loop walks them and a `static_assert` guards the layout.

The two snapshots are matched positionally, which is why a change in the device count or in a device
path is treated as an error rather than being skipped.

### Linux and Android

`/sys/block/` is walked, and a device is skipped as `virtual device` when it has no `device`
subdirectory — loop and RAM disks never appear here, unlike in `Modules/PhysicalDisk`. The name is
`vendor` + `model` with the kernel name as the fallback and the NVMe namespace suffix for
multi-namespace drives. Counters come from `/sys/block/<dev>/stat`: the field order is reads,
read-sectors-merges, write-sectors, write-merges, so the parse reads past the merged-count fields.
Sectors are multiplied by 512, which is the unit the `stat` file always uses.

### FreeBSD

`geom_gettree()` plus a devstat snapshot, restricted to rank-1 providers and skipping
`DEVSTAT_TYPE_PASS` devices. `device_type` decides the interconnect, `descr` (falling back to the
provider name) is the name, and `bytes`/`operations` for `DEVSTAT_READ` and `DEVSTAT_WRITE` are the
counters. Without `libgeom.h` at build time a different implementation using `getdevs()` is used
instead, with `device_name`+`unit_number` as the name.

### NetBSD and OpenBSD

Both use a single `sysctl`, `HW_IOSTATS` on NetBSD and `HW_DISKSTATS` on OpenBSD, after asking for
the size of the result and allocating that many bytes. NetBSD skips partitions by checking that
`/dev/<name>c` exists, because `io_sysctl` also reports partition devices; OpenBSD takes every entry,
because `diskstats` reports whole disks only. NetBSD reads `rbytes`/`rxfer` and `wbytes`/`wxfer`,
OpenBSD the `ds_`-prefixed equivalents.

### Solaris and illumos

The kstat chain is walked for entries of type `KSTAT_TYPE_IO` in class `disk`; `kstat_read()` fills a
`kstat_io_t`, from which `nread`/`reads` and `nwritten`/`writes` are taken. `devPath` is left empty
because there is no device node name to use, so `{dev-path}` renders as an empty string on this
platform.

### macOS

`IOServiceGetMatchingServices(kIOBlockStorageDriverClass)` enumerates the storage drivers; the
statistics dictionary of the driver supplies `BytesRead`/`BytesWritten`/`Reads`/`Writes`, and the
media child supplies the BSD name for `devPath`. Media names are also filtered by `namePrefix`.

### Windows

`detectPhysicalDisk()` probes `\\.\PhysicalDriveN` and `\\.\CDROMN` from index 0 upwards until
`CreateFileW()` fails, which is what ends the loop. `IOCTL_STORAGE_QUERY_PROPERTY` with
`StorageDeviceProperty` supplies the vendor and product for the name (falling back to the device
path), and `IOCTL_DISK_PERFORMANCE` supplies `BytesRead`/`BytesWritten`/`ReadCount`/`WriteCount`. A
device whose name does not match `namePrefix`, or for which the performance ioctl fails, is removed
from the list again by shrinking `result->length` — which is also why the module can end up with an
empty list and no error.
