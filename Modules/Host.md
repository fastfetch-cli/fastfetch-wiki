# Host

> Print your computer's product name

| | |
|---|---|
| Module type | `host` |
| Default order | 4 (only used by `--gen-config`) |
| Module source | `src/modules/host/host.c` |
| Detection source | `src/detection/host/` |

Prints the machine's product name, with the product version in parentheses when the firmware
provides one.

The key defaults to the module name; the key and the output both print plain text, so there is
no separate key format string here.

```
Host: MacBook Pro (16-inch, 20XX)
Host: MACHINE-TYPE-ID (Vendor Product Family Gen N)
Host: Standard PC (KVM/QEMU)
```

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `host_linux.c` | DMI (`/sys/class/dmi/id/*`), device-tree fallback, WSL/FreeBSD-compat special cases |
| Android | `host_android.c` | Android system properties |
| FreeBSD / MidnightBSD / DragonFly | `host_bsd.c` | `kenv` OIDs via `sysctl` |
| NetBSD | `host_nbsd.c` | `machdep.dmi.*` sysctls |
| OpenBSD | `host_obsd.c` | `hw.vendor` / `hw.product` sysctls |
| macOS | `host_apple.c` on arm64, `host_windows.c` on Intel | IOKit on Apple Silicon, SMBIOS on Intel Macs |
| Windows | `host_windows.c` | SMBIOS type 1 (System Information) |
| Solaris / illumos / Haiku | `host_windows.c` | SMBIOS type 1 as well |
| GNU/Hurd | `host_nosupport.c` | Reports `Not supported on this platform` |

`src/detection/host/host_mac.c` is **not** in this table because it is compiled on every
platform: it holds the hard-coded Mac model-ID table used by `host_apple.c` and, under
`#if __x86_64__`, by `host_linux.c` and `host_windows.c` as a Hackintosh fixup.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

There are no module-specific keys.

## Format string

Run `fastfetch -h host-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{family}` | Product family |
| `{name}` | Product name |
| `{version}` | Product version |
| `{sku}` | Product SKU |
| `{vendor}` | System vendor |
| `{serial}` | Product serial number |
| `{uuid}` | System UUID |

## JSON output

```jsonc
{
    "type": "host",
    "result": {
        "family": "MACHINE-TYPE-ID",
        "name": "Vendor Product Family Gen N",
        "version": "",
        "sku": "LENOVO_MT_20QF_BU_Think_FM_ThinkPad X1 Yoga 4th",
        "vendor": "LENOVO",
        "serial": "0123456789ABCDEF",
        "uuid": "00000000-0000-0000-0000-000000000000"
    }
}
```

## Examples

```jsonc
{ "type": "host", "format": "{vendor} {name}" }
```

```jsonc
{ "type": "host", "key": "Machine", "format": "{name} · {sku}" }
```

## Pitfalls

- **The default line hides most of what is detected.** Only `name` and `version` are printed;
  the vendor, SKU, serial and UUID are reachable solely through `format` or `--format json`.
- **Serial and UUID identify the machine.** They are printed by `--format json` and by
  `{serial}` / `{uuid}` without any opt-in, so a config shared as a screenshot or a bug report
  leaks them. Use `condition` or a trimmed `format` if that matters.
- **Firmware placeholders become empty.** DMI/SMBIOS strings such as `To Be Filled By O.E.M.`,
  `To be set by O.E.M.`, `System Product Name` and `None` are dropped by
  `ffCleanUpSmbiosValue()`, so a white-box board can end up with no name at all.
- **An empty result is an error, not a blank line.** If both `name` and `family` end up empty
  the module reports `neither product_family nor product_name is set by O.E.M.`
- **`{family}` and `{name}` are easy to swap.** On macOS the model ID (`MacBookProXX,Y`-style)
  lands in `family` and the human-readable name in `name`; on Windows both come from SMBIOS and
  either may be empty depending on the firmware.
- **KVM/QEMU guests are relabelled.** A DMI product name starting with `Standard PC` is
  prefixed with `KVM/QEMU `.
- **On WSL the module reports the WSL distro, not the host.** With no DMI data present it
  prints `Windows Subsystem for Linux - <distro>`, sets `family: WSL` and runs `wslinfo
  --wsl-version -n` for the version (silently skipped on older WSL builds).
- **A Linux binary running under FreeBSD's compatibility layer** reports
  `Linux Binary Compatibility on FreeBSD` instead of the hardware.
- **`{version}` is frequently empty on Apple Silicon.** `host_apple.c` reads the IOKit
  `version` property, which the newer registry entries do not always carry.
- **Android never reports a serial or UUID.** `host_android.c` fills only family, name, version
  and vendor, so `{serial}` and `{uuid}` are always empty there. It also prepends the vendor to
  the name when it is not already present, and strips a leading product name off
  `ro.product.model` before storing it as `{version}`.

## Implementation

### Linux

Two paths, selected by whether DMI gave anything:

1. `ffGetSmbiosValue("/sys/devices/virtual/dmi/id/product_name", "/sys/class/dmi/id/product_name", …)`
   for `product_name` and `product_family`; if either is present, the sibling files
   `product_version`, `product_sku`, `product_serial` and `sys_vendor` are read too.
2. Otherwise the device-tree/ARM path: `family` from `…/smbios/smbios/system/family` or
   `…/system/product`, `name` from `…/base/model` or `…/banner-name`, `serial` from
   `…/system/serial` or `…/serial-number`, `vendor` from `…/system/manufacturer`. The
   `/tmp/sysinfo/model` file is also tried and validated with `ffIsSmbiosValueSet()`.

`ffGetSmbiosValue` prefers the `/sys/devices/virtual/dmi/id/` file and falls back to
`/sys/class/dmi/id/`; it exists because `/sys/class/dmi/id` is a symlink that may be missing in
containers.

Two carve-outs follow:

- **Asahi Linux** — the DMI `product_family` is empty, so on `__aarch64__`, when the vendor is
  `Apple Inc.` and the name starts with `Mac`, `family` is *moved* from `name` and the real
  product name/serial is re-read from the device tree.
- **Hackintosh** — under `__x86_64__`, `ffHostDetectMac()` rewrites a generic DMI product name
  using the hard-coded Mac model-ID table in `host_mac.c`.

### Windows and Intel macOS (SMBIOS)

`ffGetSmbiosHeaderTable()` locates the raw SMBIOS entry point, then type 1 is walked as a
packed `FFSmbiosSystemInfo` struct. String fields (`Manufacturer`, `ProductName`, `Version`,
`SerialNumber`, `SKUNumber`, `Family`) are resolved with `ffSmbiosLocateString()` — which
returns `nullptr` for both "the firmware wrote index 0" and "no strings at all", and
`ffStrbufSetStatic()` treats `nullptr` as the empty string, so unset fields degrade quietly.
Each field is then passed through `ffCleanUpSmbiosValue()`.

`UUID` is assembled from the packed structure only when the header length proves the field
exists, and the three 16/32-bit halves are read with `FF_READ_LE` before being formatted as
`%08X-%04X-%04X-%02X%02X-%02X%02X%02X%02X%02X%02X`. `SKUNumber` and `Family` are likewise
length-gated, because they were added in SMBIOS 2.4. Three `static_assert`s pin the packed
offsets (`UUID` at 0x08, `SKUNumber` at 0x19, `Family` at 0x1A) so a compiler that changes
packing fails the build instead of corrupting the parse.

### macOS (Apple Silicon)

`ffSysctlGetString("hw.product", &family)` — falling back to `hw.model` — gives the Intel-style
model ID that Apple Silicon keeps reporting. `name` is then resolved in three steps:
the hard-coded model-ID table; failing that, `IODeviceTree:/product`'s `product-name` through
IOKit; failing that, the raw model ID. Serial, UUID, vendor and version come from the
`IOPlatformExpertDevice` registry entry (`kIOPlatformSerialNumberKey`, `kIOPlatformUUIDKey`,
`manufacturer`, `version`).

### BSD family

FreeBSD reads `kenv` keys through `sysctlbyname`; NetBSD uses the `machdep.dmi.*` namespace;
OpenBSD uses the `hw.vendor` and `hw.product` sysctls and has no serial, UUID or SKU to report.
