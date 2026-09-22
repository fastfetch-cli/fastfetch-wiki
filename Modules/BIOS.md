# BIOS

> Print first-stage bootloader information (name, version, release date, etc.)

| | |
|---|---|
| Module type | `bios` |
| Default order | 5 (only used by `--gen-config`) |
| Module source | `src/modules/bios/bios.c` |
| Detection source | `src/detection/bios/` |

Prints the firmware version, with the firmware release in parentheses when the firmware
reports one. The key carries the firmware type, so it reads `BIOS (UEFI)` or `BIOS (Legacy)`.

The key is itself a format string when you override it; it may use `{type}`, `{icon}` and
`{module-name}`.

```
BIOS (UEFI): 1.24 (2.5)
BIOS (Legacy): F8b
```

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `bios_linux.c` | DMI sysfs, falling back to the `/proc/device-tree` U-Boot version |
| Android | `bios_android.c` | Android system properties; type is always `Bootloader` |
| FreeBSD / MidnightBSD / DragonFly | `bios_bsd.c` | `kenv` |
| NetBSD | `bios_nbsd.c` | `machdep.dmi` sysctls |
| macOS | `bios_windows.c` on Intel, `bios_apple.c` on arm64 | IOKit on Apple Silicon, `IODeviceTree:/rom` on Intel |
| Windows | `bios_windows.c` | SMBIOS type 0 plus `SystemBootEnvironmentInformation` for the type |
| Solaris / illumos / Haiku / OpenBSD | `bios_windows.c` | SMBIOS type 0; the type is probed per platform |
| GNU/Hurd | `bios_nosupport.c` | Reports `Not supported on this platform` |

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | `BIOS (<type>)` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

There are no module-specific keys.

## Format string

Run `fastfetch -h bios-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{date}` | Firmware release date |
| `{release}` | Firmware release, e.g. `2.5` |
| `{vendor}` | Firmware vendor |
| `{version}` | Firmware version |
| `{type}` | Firmware type, e.g. `UEFI`, `Legacy`, `mBoot` |

## JSON output

```jsonc
{
    "type": "bios",
    "result": {
        "date": "03/12/2024",
        "release": "2.5",
        "vendor": "American Megatrends International, LLC.",
        "version": "1.24",
        "type": "UEFI"
    }
}
```

## Examples

```jsonc
{ "type": "bios", "format": "{vendor} {version}" }
```

```jsonc
{ "type": "bios", "key": "Firmware", "format": "BIOS ({type}) {version}" }
```

## Pitfalls

- **`{type}` can differ between the console and the JSON output.** `ffPrintBios` rewrites
  `BIOS` to `Legacy` *in place* on the detected structure, but only while the key is still the
  default one; `ffGenerateBiosJsonResult` re-detects and reports the raw value. On a legacy
  machine the console therefore says `BIOS (Legacy)` while `--format json` says `"BIOS"`.
  Setting a custom `key` disables the rewrite entirely, so `{type}` then reads `BIOS` again.
  This was traced in the source rather than reproduced, because it needs a legacy-boot
  machine.
- **A missing version is a hard error.** `bios_version is not set.` — the module refuses to
  print anything, even if the vendor and date were detected successfully.
- **`{release}` is rare.** Only the Linux DMI reader and SMBIOS 2.4+ on Windows provide it;
  macOS, the BSDs and Android leave it empty, so the parentheses are normally absent.
- **The type is not always `UEFI` or `BIOS`.** Apple Silicon derives it from the prefix of the
  iBoot tag (`mBoot`, `iBoot`, …) and Intel Macs report `UEFI` unconditionally; OpenBSD and
  Haiku always report `BIOS` because their SMBIOS reader is legacy-only.
- **An unknown type renders as `Unknown`.** The key becomes `BIOS (Unknown)` when the firmware
  reported no type at all — which is what a `key: "BIOS"` override is usually meant to avoid.
- **`{date}` is whatever the firmware wrote.** Linux and Windows pass the DMI string through
  verbatim, so the format varies between `03/12/2024`, `Sat Aug 8 12:42:17 PDT 2026` and
  `2024-03-12` depending on the vendor.

## Implementation

### Linux

The DMI sysfs files `/sys/devices/virtual/dmi/id/bios_{date,release,vendor,version}` are read
through `ffGetSmbiosValue()` (with the `/sys/class/dmi/id/` path as fallback). If `bios_date`
cannot be read at all, the reader falls back to the device-tree
`/proc/device-tree/chosen/u-boot,version`, trims the trailing NUL and hard-codes the vendor to
`U-Boot`.

The firmware type is decided purely by path existence: `/sys/firmware/efi/` as a directory, or
`/sys/firmware/acpi/tables/UEFI` as a file, means `UEFI`; anything else is `BIOS`.

### Windows, Solaris, Haiku, OpenBSD (SMBIOS)

Type 0 is walked as a packed `FFSmbiosBios`: `Vendor`, `BiosVersion` and `BiosReleaseDate` are
string indices resolved through `ffSmbiosLocateString()` and cleaned with
`ffCleanUpSmbiosValue()`; the three strings with a literal index of `0` yield an empty field
instead of a stale pointer. `{release}` is built as `%u.%u` from
`SystemBiosMajorRelease` / `SystemBiosMinorRelease`, and only when the header length shows the
SMBIOS 2.4 fields are present. A `static_assert` pins `ExtendedBiosRomSize` at offset 0x18.

The firmware type is platform-specific:

- **Windows** — `NtQuerySystemInformation(SystemBootEnvironmentInformation)` gives
  `FirmwareTypeBios` / `FirmwareTypeUefi`. This is used instead of `GetFirmwareType()` so the
  module still works on Windows 7.
- **Solaris** — `di_init("/", DINFOPROP)` plus a lookup of the `efi-version` property.
- **Haiku / OpenBSD** — unconditionally `BIOS`, with the comment that their SMBIOS detection is
  legacy-only.
- **Intel macOS** — the presence of `IODeviceTree:/efi` in the IORegistry.

### macOS (Apple Silicon)

Intel Macs are handled by the SMBIOS reader above and report `UEFI` from the day one note in
the source. On arm64 `bios_apple.c` reads the vendor and date from `IODeviceTree:/` directly.
The version and type are *split* from the `iboot-stage-one-tag` (falling back to
`system-firmware-version`) property of `IODeviceTree:/chosen`: everything before the first `-`
becomes `{type}` and the remainder becomes `{version}`, so a tag of `mBoot-9.9.9` yields
`type: mBoot` and `version: 9.9.9`. When the tag has no `-`, or the property is missing,
the type defaults to `iBoot`.

### Android, FreeBSD, NetBSD

**Android** reads the `ro.bootloader` property, falling back to `ro.boot.bootloader`, and
clears the value when the firmware wrote the literal string `unknown` — which then makes the
module fail with `bios_version is not set.` Its `{type}` is always `Bootloader`, so the key
reads `BIOS (Bootloader)`.

**FreeBSD** takes the four fields from `kenv` (`smbios.bios.reldate`, `.revision`, `.vendor`,
`.version`) and decides `{type}` in three steps: the `machdep.bootmethod` sysctl; failing that
`loader.efi`, mapped to `UEFI`/`BIOS` by comparing it with `1`; failing that the existence of
`/dev/efi` or the `/boot/efi/efi/` directory. The source notes that `/boot/efi` exists even on
BIOS systems, which is why it tests the deeper `efi/` path.

**NetBSD** reads `machdep.dmi.bios-{date,version,vendor}` and takes `{type}` from
`machdep.bootmethod` as well. It branches on the return value rather than on `length == 0` as
FreeBSD does: `ffSysctlGetString()` reports an error both for a missing key and for an empty
one, so the two forms are equivalent, and the type is guessed from `/dev/efi` in exactly the
same situation. When the key does hold a value it is used verbatim, so `{type}` shows whatever
NetBSD reports rather than a normalised `UEFI`/`BIOS`.
