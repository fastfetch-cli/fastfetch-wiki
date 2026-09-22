# PhysicalDisk

> Print physical disk information

| | |
|---|---|
| Module type | `physicaldisk` |
| Default order | 69 (only used by `--gen-config`) |
| Module source | `src/modules/physicaldisk/physicaldisk.c` |
| Detection source | `src/detection/physicaldisk/` |

Prints one line per physical device — a disk, not a mounted volume. Where `Modules/Disk` reports
file-system usage per mount point, this module reports the hardware behind it: model name, capacity,
whether it is an SSD or a rotating disk, whether it is removable, whether the medium is read-only
and, when asked, its temperature. The key is `Physical Disk (<device name>)`, falling back to the
device path when the name is empty.

```
Physical Disk (NVMe SSD 1TB): 931.84 GiB [SSD, Fixed]
Physical Disk (USB Flash Disk): 57.30 GiB [HDD, Removable]
Physical Disk (Apple disk image Media): 3.07 GiB [Virtual, Removable, Read-only]
```

The size goes through `display.size`. The bracket lists the type flags that are set, in the order
`Virtual`/`HDD`/`SSD`, then `Removable`/`Fixed`, then `Read-only`; there is no label for the
`Read-write` case in the default output. With `temp: true` a temperature is appended after the
bracket, separated by ` - `.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `physicaldisk_linux.c` | Walks `/sys/block/` |
| Android | `physicaldisk_linux.c` | Same file |
| GNU/Hurd | `physicaldisk_nosupport.c` | Reports "Not supported on this platform" |
| FreeBSD | `physicaldisk_bsd.c` | libgeom providers plus a devstat snapshot |
| NetBSD | `physicaldisk_nbsd.c` | `hw.disknames` plus SCSI inquiry; needs root |
| OpenBSD | `physicaldisk_obsd.c` | `hw.disknames` plus SCSI inquiry; needs root |
| Solaris / illumos | `physicaldisk_sunos.c` | Walks the device tree with libdevinfo |
| Haiku | `physicaldisk_haiku.c` | Recursively looks for `raw` device files under `/dev/disk` |
| macOS | `physicaldisk_apple.c` | IOKit `IOBlockStorageDriver` registry walk |
| Windows | `physicaldisk_windows.c` | Device interfaces from `cfgmgr32` plus `DeviceIoControl` |

GNU/Hurd is the only platform without an implementation.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `namePrefix` | string | empty | Only show devices whose name starts with this string |
| `hideVirtual` | boolean | `false` | Hide virtual disks (loop, RAM, file-backed, device-mapper, …) |
| `hideUnused` | boolean | `true` | Hide devices with `size == 0`, i.e. no medium or nothing allocated |
| `temp` | boolean or object | `false` | Detect and print the temperature, if the platform supports it. An object sets the colour thresholds: `{ "green": 50, "yellow": 70 }` |
| `key` | string | `Physical Disk (<name>)` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

`temp: null` is the same as `temp: false`; a wrong type is a fatal error, not a warning — see the
Pitfalls. The colour thresholds follow `display.temp.unit`, `display.temp.ndigits` and the
`display.temp.color` palette, so `{temperature}` honours a unit switch to Fahrenheit or Kelvin.

## Format string

Run `fastfetch -h physicaldisk-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{name}` | Device name — also available in key format |
| `{dev-path}` | Device file path (`/dev/nvme0n1`, `\\?\scsi#…` on Windows) — also available in key format |
| `{size}` | Device capacity, formatted with `display.size` |
| `{interconnect}` | Interconnect type (`NVMe`, `SATA`, `USB`, `Apple Fabric`, …) |
| `{serial}` | Serial number; empty when the platform does not expose one |
| `{physical-type}` | `Virtual`, `HDD` or `SSD`; empty when the platform cannot tell |
| `{removable-type}` | `Removable` or `Fixed`; empty when unknown |
| `{readonly-type}` | `Read-only` or `Read-write`; empty when unknown |
| `{revision}` | Product revision / firmware level |
| `{temperature}` | Temperature, colon-free and unit-aware (`33.0°C`); empty when unavailable |

In key format only `{name}` and `{dev-path}` carry device data; `{index}`, `{icon}` and
`{module-name}` are available as in every module. `{index}` is `0` when a single device is printed
and otherwise a 1-based counter.

## JSON output

```jsonc
{
    "type": "PhysicalDisk",
    "result": [
        {
            "name": "NVMe SSD 1TB",
            "devPath": "/dev/nvme0n1",
            "interconnect": "NVMe",
            "kind": "SSD",
            "size": 1000204886016,
            "serial": "0123456789ABCDEF",
            "removable": false,
            "readOnly": false,
            "unknown": false,
            "revision": "FW 1.2.3",
            "temperature": 33.0
        }
    ]
}
```

`size` is raw bytes and `temperature` is Celsius as a real number. `kind`, `removable` and
`readOnly` are `null` when the platform could not determine them. On failure the object is
`{ "type": "PhysicalDisk", "error": "…" }`.

## Examples

```jsonc
// Only the system's own NVMe disks, no USB enclosures
{ "type": "physicaldisk", "namePrefix": "NVMe", "hideVirtual": true }
```

```jsonc
// Show the temperature with a custom palette
{ "type": "physicaldisk", "temp": { "green": 40, "yellow": 55 } }
```

```jsonc
// The default output, rebuilt by hand
{ "type": "physicaldisk", "format": "{size} [{physical-type}, {removable-type}]" }
```

```jsonc
// Everything the platform knows, as a table row
{ "type": "physicaldisk", "key": "{index} {name}", "format": "{dev-path} {interconnect} {serial} {revision}" }
```

## Pitfalls

- **`namePrefix` does not filter virtual devices on Linux.** The comparison lives inside the branch
  that has already opened the device's `/sys` directory, so loop and RAM disks — which have no
  `device` subdirectory — bypass it entirely. `"namePrefix": "zzz"` on a machine with 16 RAM disks
  prints those 16 lines and nothing else, because the real disks were filtered out correctly.
- **`unknown` in the JSON result means "size == 0", not "detection failed".** It mirrors
  `hideUnused`: the flag behind it is the module's `UNUSED` bit, so a card reader with no card in it
  reports `"unknown": true` and `"size": 0`. A consumer that treats `unknown` as an error will
  misreport empty drives.
- **The JSON array is not sorted, the text output is.** The alphabetical sort by name happens in the
  printing path only, so `--format json` returns the platform's detection order — on Linux that is
  `readdir()` order over `/sys/block`, which starts at `loop0` and has no relation to the text order.
- **`{readonly-type}` says `Read-write` where the default output says nothing.** The default line
  prints the read-only label only when the medium really is read-only, so a healthy disk shows
  `[SSD, Fixed]`; in a custom format the same device yields `Read-write`. Do not use the presence of
  `Read-write` in a format as evidence that a flag was set.
- **On Linux the name can be a PCI vendor ID.** The name is `vendor` + `model` from the device's
  `/sys` directory with nothing else to fall back on. For a virtio disk both files exist but only
  `vendor` has content (`0x1af4`), so two virtual disks print the same key and only `{dev-path}`
  tells them apart. The same happens on any driver that reports a vendor but no model.
- **`hideUnused: false` is noisy on Linux.** Every loop device has `size == 0` until a file is
  attached, so the option adds one `0 B [Virtual, Fixed]` line per loop device.
- **A wrong `temp` value aborts the whole run, and `display.showErrors` does not hide it.** The
  message goes to stderr and the process exits with status 224 (`exit(480)`); `"temp": "yes"` fails
  even when errors are supposed to be silent. An unknown *key* is the opposite: `ffPrintError()`
  drops it unless `display.showErrors` is true.
- **`temp: true` is not a promise.** The temperature is fetched from a different interface than the
  rest of the data — NVMe SMART on macOS, `hwmon` on Linux, `IOCTL_STORAGE_QUERY_PROPERTY` on
  Windows — and silently stays empty when that interface is missing, so a desktop HDD or a disk
  behind a USB bridge prints no temperature at all.
- **NetBSD and OpenBSD need root.** Opening a raw disk to run the SCSI inquiry fails with `EACCES`
  and the module aborts with `Permission denied; root required`. It also aborts the whole module, not
  just that one device, so an unprivileged run loses the list of disks entirely.
- **FreeBSD without `libgeom` loses the module.** `physicaldisk_bsd.c` is compiled against
  `libgeom.h` when it is available; without it the file still builds but the detector returns
  `Fastfetch was compiled without libgeom support` for the module as a whole.
- **Haiku never classifies the medium.** The Haiku detector fills in size, interconnect and the
  removable/read-only flags, but never sets the SSD/HDD bit, so `{physical-type}` is empty and
  `kind` is `null` there. It also skips `/dev/disk/virtual`, so virtual devices are missing rather
  than hidden — `hideVirtual` has no effect on this platform.
- **`{size}` is the device size, not the partition size.** The module never looks at partitions or
  file systems; use `Modules/Disk` for how much of a disk is in use.

## Implementation

`ffDetectPhysicalDisk()` returns a list; `ffPrintPhysicalDisk()` sorts it by name and prints one line
per device, while `ffGeneratePhysicalDiskJsonResult()` serialises the unsorted list. Nothing is
cached, so the module re-probes the hardware on every `--dynamic-interval` round.

The type bits are a `uint8_t` mask: `HDD`/`SSD`/`Virtual`, `Fixed`/`Removable`, `Readwrite`/
`Readonly` and `Unused`. A detector sets whichever of them it can determine; a bit that is never set
becomes an empty string in the format output and `null` in the JSON result. `size == 0` is how a
platform reports "no medium or nothing allocated", and it is the only thing `hideUnused` looks at.

### Linux and Android

`/sys/block/` is walked with `openat()`, keeping directory descriptors instead of paths. The size is
`size × 512` — the `size` file is always in 512-byte sectors, regardless of the real sector size.
`/sys/block/<dev>/device` missing means a virtual device: the module adds the `Virtual` bit and sets
the interconnect to `Virtual`. Otherwise the name is `vendor` plus `model` (falling back to the kernel
device name when both are empty), and for NVMe namespaces that share one device the namespace id is
appended (`NVMe SSD - 2`).

The interconnect comes from the real path of `/sys/block/<dev>/device`: `/usb` → `USB`, `/ata` →
`ATA`, `/scsi` → `SCSI`, `/nvme` → `NVMe`, `/virtio` → `VirtIO`, otherwise the content of the
`transport` file. `nvme*` and `mmcblk*` names short-circuit to `NVMe` and `MMC`. `VirtIO` is a
special case: the disk is virtual but still worth reporting, so the bit is not set and, because the
`rotation` file means nothing there, the SSD/HDD probe, the serial, the firmware revision and the
temperature are all skipped for it.

Temperature is read from `hwmon0`…`hwmon9` inside the device directory (`temp1_input`, in
millidegrees), which is why it works for NVMe and SATA disks with a `hwmon` node and not for USB
bridges. Rotational flags come from `queue/rotational`, the serial from `device/serial`, the firmware
from `device/firmware_rev` or `device/rev`, and the removable and read-only flags from
`/sys/block/<dev>/removable` and `/ro`.

### FreeBSD

`geom_gettree()` builds the provider tree and `geom_stats_open()`/`geom_stats_snapshot_get()` supply
a devstat snapshot used for the interconnect (`DEVSTAT_TYPE_IF_SCSI`/`IDE`/`OTHER`/`NVME`). Only
rank-1 providers of a non-`DISK` class are `Virtual`; `rotationrate` gives SSD (`0`) or HDD,
`access` gives read-only versus read-write (with `lg_mode`'s `rNwNeN` string as the fallback), and
`ident` is the serial. `descr` is preferred as the name and the provider name is the fallback.

### NetBSD and OpenBSD

Both read the space-separated (`hw.disknames`) or comma-separated (`HW_DISKNAMES`) list of disk
names, open each one with `opendisk()`/`opendev()`, and take the geometry from `DIOCGDINFO` or
`DIOCGPDINFO` with NetBSD's `DIOCGDISKINFO` proplib dictionary as the richer first choice. The
device name is then replaced by a SCSI `INQUIRY` (vendor + product, revision), and a second inquiry
reads the unit-serial VPD page for the serial; a `MODE SENSE` page supplies the read-only bit.
NetBSD recognises its virtual disk types (`DKTYPE_VND`, `LD`, `RAID`, `CGD`, `VINUM`, `DM`, `RUMPD`,
`MD`), OpenBSD only `DTYPE_VND` and `DTYPE_RDROOT`. Both set the interconnect from the
disklabel's type name. SCSI sense data is logged under `FF_DEBUG` only.

### Solaris and illumos

`di_init("/", DINFOCPYALL)` plus `di_walk_minor(DDI_NT_BLOCK)` visits every block minor; only
character minors named `a,raw` are kept, because those are the whole-disk nodes. The name is
`inquiry-vendor-id` plus `inquiry-product-id`, the size is `device-nblocks × device-blksize`,
`device-solid-state` gives SSD or HDD, and `inquiry-device-type` gives `DTYPE_DIRECT` → read-write or
`DTYPE_RODIRECT` → read-only. The serial is `inquiry-serial-no`, or `usb-serialno` from the parent
node (which also marks the interconnect as `USB`); otherwise the interconnect is the device's
`class` property or the parent's `model`.

### Haiku

`/dev/disk` is walked recursively looking for files called `raw`, skipping the `virtual` directory.
Each one is opened and queried with `B_GET_GEOMETRY` for the size (`cylinders × heads × sectors ×
bytes-per-sector`) and `B_GET_DEVICE_NAME` for the name — which fails with "not a tty" on NVMe
drives, in which case the name becomes `Unknown <type> drive` using the directory the device was
found in. The interconnect is that same directory name (`ata`, `usb`, `nvme`, …), and `raw` paths are
used as `devPath`. Only the read-only and removable bits are derived; the medium type is never
probed, so Haiku always reports an empty `{physical-type}`.

### macOS

`IOServiceGetMatchingServices(kIOBlockStorageDriverClass)` walks the storage drivers, and for each
one the child in the service plane (the media) supplies name, size, `kIOMediaRemovableKey`,
`kIOMediaWritableKey` and `kIOBSDNameKey` for `devPath`, while the parent supplies
`kIOPropertyProtocolCharacteristicsKey` (the interconnect, plus ` - <location>` for virtual devices)
and `kIOPropertyDeviceCharacteristicsKey` (serial, revision, and the SSD/HDD medium type — which is
deliberately not read for virtual devices). Temperature needs `temp: true`, an
`kIOPropertyNVMeSMARTCapableKey` device and a macOS 10.15 SDK: `NVMeSMARTData.TEMPERATURE` is in
Kelvin and has 273 subtracted from it.

### Windows

`CM_Get_Device_Interface_ListW()` enumerates the `GUID_DEVINTERFACE_FLOPPY`, `DISK`, `CDROM` and
`TAPE` device interfaces; each is opened with `CreateFileW()` and described by three ioctls —
`IOCTL_DISK_GET_DRIVE_GEOMETRY_EX` for the size (falling back to `IOCTL_DISK_GET_DRIVE_GEOMETRY`, whose
media type also names floppy disks and marks them `HDD`/`Removable`), `IOCTL_STORAGE_QUERY_PROPERTY`
with `StorageDeviceProperty` for bus type, vendor, product, serial and revision, and
`StorageDeviceSeekPenaltyProperty` for SSD versus HDD. The removable flag comes from
`STORAGE_DEVICE_DESCRIPTOR.RemovableMedia`, the read-only flag from
`IOCTL_STORAGE_GET_MEDIA_TYPES_EX`, and `StorageDeviceTemperatureProperty` supplies the temperature
for anything that is neither virtual nor detected as a floppy. `StorageDeviceProperty`'s result is
used behind a `DeviceIoControl(...) || retSize == 0` condition, where `retSize` is still uninitialised
if the earlier geometry ioctl also failed — a dropped media or a driver that rejects the query can
therefore be described from an uninitialised buffer, so treat the property-derived fields of an odd
device as unreliable.
