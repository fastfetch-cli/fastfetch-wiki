# Disk

> Print partitions, space usage, file system, etc

| | |
|---|---|
| Module type | `disk` |
| Default order | 42 (only used by `--gen-config`) |
| Module source | `src/modules/disk/disk.c` |
| Detection source | `src/detection/disk/` |

Prints one line per mounted volume: used and total size, the percentage in use, the file system and
any volume-type labels. The key is `Disk (<mount point>)`, so every volume is labelled with the
mount point it belongs to — a custom `key` is shared by all of them, and `{index}` is the only way to
tell the lines apart.

```
Disk (/): 45.30 GiB / 100.00 GiB (45%) - ext4
Disk (/boot): 300.00 MiB / 1024.00 MiB (29%) - vfat [External]
Disk (/srv/data): Unknown - zfs [Subvolume]
```

The sizes go through `display.size` (so `binaryPrefix: si` prints `48.64 GB`), the percentage is
styled by this module's `percent` thresholds, and the bracket at the end lists the volume-type flags
that are set — `External`, `Subvolume`, `Hidden`, `Read-only`, in that order and comma separated. A
volume whose total size cannot be read prints the literal word `Unknown` instead of `used / total`
(and gets the `Unknown` type in the JSON result, which has no label of its own in the text output).

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `disk_linux.c` | `/proc/mounts` plus `statvfs()`; birth time via `statx()` |
| Android | `disk_linux.c` | Same file, with the `/dev` checks compiled out |
| GNU/Hurd | `disk_linux.c` | Same file |
| FreeBSD | `disk_bsd.c` | `getfsstat()`; volume labels need `libgeom` at build time |
| NetBSD | `disk_bsd.c` | Same file, switched to `getvfsstat()` |
| OpenBSD | `disk_bsd.c` | Same file; birth time comes from `__st_birthtim` |
| Solaris / illumos | `disk_sunos.c` | `/etc/mnttab`; the volume name is the `fstr` of the mount |
| Haiku | `disk_haiku.cpp` | `next_dev()` / `fs_stat_dev()` |
| macOS | `disk_bsd.c` | `getfsstat()`; volume name via `getattrlist()` |
| Windows | `disk_windows.c` | `NtQueryInformationProcess(ProcessDeviceMap)` and `NtQueryVolumeInformationFile` |

All ten platforms have a real implementation, so the module never reports "Not supported on this
platform". There is no `disk_nosupport.c` in the tree.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `folders` | string or array | empty — every mount point | Restrict the output to these mount points. Overrides every `show*` option. |
| `hideFolders` | string or array | `/efi:/boot:/boot/*` on Linux, the BSDs, Solaris and Haiku; empty on macOS, Windows and Android | Mount points or `fnmatch` glob patterns to hide |
| `hideFS` | string or array | empty | File system names to hide (`apfs`, `ext4`, `tmpfs`, `drvfs`, …) |
| `showRegular` | boolean | `true` | Show regular volumes |
| `showExternal` | boolean | `true` | Show external / removable / network volumes |
| `showHidden` | boolean | `false` | Show hidden volumes |
| `showSubvolumes` | boolean | `false` | Show subvolumes (btrfs subvolumes, ZFS datasets) |
| `showReadOnly` | boolean | `true` | Show read-only volumes |
| `showUnknown` | boolean | `false` | Show volumes whose sizes cannot be detected |
| `useAvailable` | boolean | `false` | Compute used bytes from `f_bavail` (Windows: `CallerAvailableAllocationUnits`) instead of `f_bfree` |
| `percent` | object | `{ "green": 50, "yellow": 80, "type": 0 }` | Colour thresholds and style for the percentage. `type: 0` inherits `display.percentType`. |
| `key` | string | `Disk (<mount point>)` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

`folders`, `hideFolders` and `hideFS` accept either a separated string or an array of strings. The
separator is `:` on POSIX and `;` on Windows. Entries in `hideFolders` are `fnmatch` patterns, so
`/run/*` hides every mount point below `/run`.

The default `showTypes` mask is `showRegular | showExternal | showReadOnly` — the module is built
around the assumption that the volumes you want to see are the regular, local, mounted ones,
whether or not they happen to be read-only. macOS makes that concrete: the sealed system volume at
`/` is read-only, and hiding read-only volumes by default would hide the root file system.

## Format string

Run `fastfetch -h disk-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{size-used}` | Used size, formatted with `display.size` |
| `{size-total}` | Total size, formatted |
| `{size-free}` | Free size (`f_bfree`), formatted |
| `{size-available}` | Size available to an unprivileged user (`f_bavail`), formatted |
| `{size-percentage}` | Used percentage as a number; empty unless the percentage style includes the number |
| `{size-percentage-bar}` | Used percentage as a bar, brackets included; empty unless the percentage style includes a bar |
| `{files-used}` | Inodes used |
| `{files-total}` | Inodes total |
| `{files-percentage}` | Inode usage as a number; empty unless the number style is enabled |
| `{files-percentage-bar}` | Inode usage as a bar; empty unless the bar style is enabled |
| `{filesystem}` | File system name — also available in key format |
| `{name}` | Volume label / name — also available in key format |
| `{mountpoint}` | Mount point — also available in key format |
| `{mount-from}` | Source of the mount (device path, or `zroot/ROOT/default` on ZFS) — also available in key format |
| `{is-external}` | `true` if the volume is external |
| `{is-hidden}` | `true` if the volume is hidden |
| `{is-readonly}` | `true` if the volume is read-only |
| `{create-time}` | Creation time in local time, `YYYY-MM-DD hh:mm:ss`; empty when unknown |
| `{years}`, `{days-of-year}`, `{years-fraction}` | Age of the volume as whole years, the day within the current year, and a fractional year (`0.05`) |
| `{days}`, `{hours}`, `{minutes}`, `{seconds}`, `{milliseconds}` | Age broken down into days, hours, minutes, seconds and milliseconds |

### Variables available only in key format

The key format string is parsed separately, so it has three names that the output format does not
have:

| Variable | Description |
|---|---|
| `{index}` | 1-based counter over the volumes that are actually printed |
| `{mountpoint-link}` | The mount point wrapped in an `OSC 8` hyperlink pointing at `file://<mount point>` |
| `{name-link}` | The volume name wrapped in the same hyperlink |

Inside WSL with Windows Terminal (`WSL_DISTRO_NAME` and `WT_SESSION` both set) the links are rewritten
to `file:///<drive>:/` for 9p mounts and to `file:////wsl.localhost/<distro><path>` otherwise, so
clicking the key opens the volume in Explorer. Everywhere else the link target is just
`file://<mount point>`, which most terminals treat as a local path.

## JSON output

```jsonc
{
    "type": "Disk",
    "result": [
        {
            "bytes": { "available": 160183455744, "free": 160183455744, "total": 900000120832, "used": 739816665088 },
            "files": { "total": 1564775574, "used": 484014 },
            "filesystem": "apfs",
            "mountpoint": "/",
            "mountFrom": "/dev/disk1s1s1",
            "name": "System",
            "volumeType": ["Regular", "Read-only"],
            "createTime": "2024-03-01T12:00:00.000+0800"
        }
    ]
}
```

All byte values are raw numbers, `volumeType` is an array of strings in the order
`Regular`, `External`, `Subvolume`, `Hidden`, `Read-only`, `Unknown`, and `createTime` is either the
full ISO-8601 stamp or `null`. `files.total` and `files.used` are both `null` when the file system
does not report inode counts at all (the Windows file system in WSL returns `0`/`0`, which is turned
into `null`). On a detection failure the object is `{ "type": "Disk", "error": "…" }`.

## Examples

```jsonc
{ "type": "disk", "folders": "/", "format": "{size-used} / {size-total} ({size-percentage})" }
```

```jsonc
// Show only the root file system, with a bar
{
    "type": "disk",
    "folders": "/",
    "percent": { "type": ["num", "bar"] },
    "format": "{size-percentage-bar} {size-used} / {size-total}"
}
```

```jsonc
// Hide everything on a network file system, and the ESP
{ "type": "disk", "hideFS": "nfs:nfs4:cifs", "hideFolders": "/efi:/boot/*" }
```

```jsonc
// Make every line clickable, and show the volume label as the key
{ "type": "disk", "key": "{mountpoint-link}", "format": "{name} {size-used} / {size-total}" }
```

## Pitfalls

- **`folders` matches mount points, not directories.** The entries are compared against the mount
  point string for equality, so `"/home/user"` finds nothing when `/home` is the mount point, and
  `"/"` finds the root file system. The presets follow this rule (`"/"`,
  `"/:/home:/boot:/efi"`, and on macOS `"/System/Volumes/VM"` to reach a volume that is hidden by
  default).
- **`folders` silently disables every `show*` option.** The filter only runs when `folders` is
  empty, which is what the schema means by "This option overrides other `show*` options": with
  `"folders": "/"` you get `/` even if you also wrote `"showRegular": false`.
- **`show*` filters are applied to the text output only.** `--format json` calls the same detector
  but never applies the mask, so `"showRegular": false` prints nothing on screen while the JSON
  result still lists every regular volume. `folders`, `hideFolders` and `hideFS` *are* honoured by
  both, because they are applied during detection.
- **The filters run after the key is built and are completely silent.** A configuration that hides
  everything prints no line and no error — the module still reports success. Do not read "no output"
  as "not supported".
- **The default `showReadOnly` is `true`, not `false`.** `doc/json_schema.json` claims `false`; the
  initialiser adds the read-only bit to the default mask, and read-only volumes appear in the
  default output.
- **`hideFolders` has a platform-dependent default.** On Linux, the BSDs, Solaris, Haiku and
  GNU/Hurd it is `/efi:/boot:/boot/*`, which hides the ESP and the boot partition — so a machine
  with a separate `/boot` prints one line where `lsblk` shows two. On macOS, Windows and Android it
  is empty. An explicit `hideFolders` replaces the default, so hiding one extra directory means
  listing the boot patterns again.
- **A run with `--format json` and no `folders` includes volumes the text output never shows** —
  every hidden APFS helper volume, every `Unknown` volume, and (on Windows) every letter that maps
  to a drive with no media.
- **`{size-percentage}` and `{size-percentage-bar}` can be empty.** Both are computed only when the
  active percentage style asks for them: the number needs the `num` flag and the bar needs a bar
  flag. The display default is `["num", "num-color"]`, so the number works and the bar renders as an
  empty string until `percent.type` (or `display.percentType`) adds `"bar"`. `{size-percentage-bar}`
  brings its own `[` `]`, so `[{size-percentage-bar}]` prints `[[ ■■■■■■■■-- ]]`.
- **On macOS, `/` and its data volume report the same total and free figures.** APFS shares one
  free-space pool between the volumes of a container, and `f_bfree` is a property of the container,
  so `Disk (/)` and `Disk (/System/Volumes/Data)` contain the same numbers (only `used` differs, by
  whatever was written between the two `statvfs()` calls). The data volume is typed `Hidden`, so the
  default configuration prints `/` only. `useAvailable` exists for the same reason: on macOS
  `f_bfree` counts the APFS reserved blocks, and the schema suggests it "may be required for macOS
  to display correct results" when the numbers disagree with Finder.
- **`f_bfree` and `f_bavail` differ, and the module prints both.** `{size-free}` is everything the
  file system considers free; `{size-available}` is what an unprivileged process may still write.
  They differ whenever the file system holds something back — ext4's reserved blocks, a ZFS
  reservation — and `used` is `total − free` unless `useAvailable` is set. On a file system with
  nothing reserved (a container's plain ext4, for instance) the two differ only by the few blocks
  written between the two readings.
- **The `Read-only` label on macOS does not mean a read-only disk.** It comes from `MNT_RDONLY`,
  which macOS sets for the sealed system snapshot at `/` on every modern install.
- **`Unknown` is a size, not an error.** When `statvfs()` fails (a share whose server went away, a
  drive with no media), the volume keeps `bytesTotal == 0`, gets the `Unknown` type bit and prints
  `Unknown` in place of the sizes. It is hidden by default (`showUnknown: false`) but still appears
  in the JSON result.
- **`Unknown` in `volumeType` has no counterpart in the text output.** The bracket only knows
  `External`, `Subvolume`, `Hidden` and `Read-only`; the `Unknown` bit shows up in the JSON array
  and as the missing sizes. A parser that maps `volumeType` to the bracket list will not round-trip.

## Implementation

Every platform implements only `ffDetectDisksImpl()`; the shared `disk.c` then sorts the list
alphabetically by mount point — which is also the order the volumes are printed in — marks volumes
with `bytesTotal == 0` as `Unknown` and fills in `bytesUsed` as
`bytesTotal − (free | available)` according to `useAvailable`. The per-volume printing, the key, the
`show*` mask and the JSON writer all live in the module.

There is no cache in this module: `ffPrintDisk()` and `ffGenerateDiskJsonResult()` each call the
detector, so under `--dynamic-interval` the volumes are re-read on every round. The one piece of
state that survives a call is FreeBSD's `geom_gettree()` result, which is deliberately reused
because the geom tree is expensive to build.

### Linux, Android and GNU/Hurd

`/proc/mounts` is read with `setmntent()`/`getmntent()`. Each entry is tested by `isPhysicalDevice()`
unless `folders` was given: the root mount is always kept, `none` sources, pseudo file
systems without a `/dev/` source, `loop*`, `ram*` and `fd*` devices and Bedrock Linux's
`/bedrock/*` mounts are dropped, and everything else must stat as a block device. 9p mounts are kept
only when their options contain `aname=drvfs` — that is the WSL interop mount — while `zfs`,
`bcachefs` and `fuse.sshfs` are always kept. On Android the `/dev` checks are skipped (the directory
is not readable there) and `/apex/*` is dropped instead.

The volume name is looked up by `stat()`ing the device and scanning `/dev/disk/by-label/` and then
`/dev/disk/by-partlabel/` for a directory entry with the same inode number; the name is then run
through `ffStrbufDecodeHexEscapeSequences()` to turn `\x20` escapes into spaces. The type is
`Hidden` for `x-gvfs-hide`/`hidden` mount options, `Subvolume` when a device already appeared
earlier in the sorted list (btrfs subvolumes, ZFS datasets), `External` when
`/sys/class/block/<dev>/removable` is `1`, and `Regular` otherwise; `ro` adds the `Read-only` bit.
Sizes come from `statvfs()`, and the creation time from a `statx()` `STATX_BTIME` call, rejected when
it is before 1991 (the field is not always real).

### FreeBSD, NetBSD, OpenBSD and macOS

`getfsstat()` (NetBSD: `getvfsstat()`) returns the mount table. Without `folders`, only `/`, `/dev/`
sources, `zfs` and `fusefs.sshfs` are kept. FreeBSD reads volume labels out of the geom tree
(`geom_gettree()`, class `LABEL`) and needs `libgeom` available at build time — otherwise the label
is silently left empty. NetBSD's `statvfs` has signed counters and its `f_flags`/`f_frsize` are
aliased to the `statfs` names.

On macOS the name comes from `getattrlist(ATTR_CMN_NAME)` on the mount point, and the type is
`Hidden` for `MNT_DONTBROWSE`, `External` for `MNT_REMOVABLE` or a non-local mount, `Regular`
otherwise. The creation time is `st_birthtimespec`, which DragonFly BSD does not have.

### Solaris and illumos

`/etc/mnttab` is parsed with `getmntent()`. Without `folders`, only `/`, `zfs` and block devices
under `/dev/` are kept. `staymntopt(MNTOPT_NOBROWSE)` makes a volume `Hidden`, `MNTOPT_RO` adds
`Read-only`; there is no `External` detection on this platform. The volume name is the `fstr` field
of the `statvfs` result, trimmed.

### Haiku

`next_dev()` walks the device list, `fs_stat_dev()` fills in the block and node counters and
`BDirectory`/`BPath` turn the root node into a mount-point path. `B_FS_IS_PERSISTENT` unset means
`Hidden`, `B_FS_IS_READONLY` means `Read-only`, `B_FS_IS_REMOVABLE` means `External`, and a volume
with none of them is `Regular`. The creation time comes from `BDirectory::GetCreationTime()`.

### Windows

`NtQueryInformationProcess(ProcessDeviceMap)` returns the drive map and the type of each letter;
`DRIVE_NO_ROOT_DIR` is skipped unless `folders` asked for it. Each drive is opened with
`FILE_FLAG_BACKUP_SEMANTICS` and queried three times: `FileFsAttributeInformation` gives the file
system name and the read-only flag, `FileFsVolumeInformation` gives the label and the creation time,
and `FileFsFullSizeInformation` gives total, free and caller-available sizes from the allocation
unit counts. `mountFrom` is the DOS device path from `QueryDosDeviceW()`. A `folders` value of
`"/"` is rewritten to `<system drive>:\` before matching, which is what makes the cross-platform
presets work.
