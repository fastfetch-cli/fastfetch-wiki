# Chassis

> Print chassis type information (desktop, laptop, etc.)

| | |
|---|---|
| Module type | `chassis` |
| Default order | 8 (only used by `--gen-config`) |
| Module source | `src/modules/chassis/chassis.c` |
| Detection source | `src/detection/chassis/` |

Prints the chassis type (`Laptop`, `Desktop`, `All in One`, …), with the chassis version in
parentheses when the firmware reports one.

```
Chassis: Laptop
Chassis: Mini PC (1.0)
```

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `chassis_linux.c` | DMI sysfs `/sys/class/dmi/id/chassis_type`, device-tree fallback |
| FreeBSD / MidnightBSD / DragonFly | `chassis_bsd.c` | `smbios.chassis.*` `kenv` OIDs |
| NetBSD | `chassis_nbsd.c` | `machdep.dmi.chassis-*` sysctls |
| macOS | `chassis_apple.c` on arm64, `chassis_windows.c` on Intel | Derived from the Mac model name on Apple Silicon |
| Windows | `chassis_windows.c` | SMBIOS type 3 (System Enclosure) |
| Solaris / illumos / Haiku / OpenBSD | `chassis_windows.c` | SMBIOS type 3 as well |
| Android | `chassis_nosupport.c` | Reports `Not supported on this platform` |
| GNU/Hurd | `chassis_nosupport.c` | Reports `Not supported on this platform` |

`src/detection/chassis/chassis.c` — the `ffChassisTypeToString()` table — is compiled on every
platform and is therefore not part of the per-platform mapping.

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

Run `fastfetch -h chassis-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{type}` | Chassis type, e.g. `Laptop` |
| `{vendor}` | Chassis vendor |
| `{version}` | Chassis version |
| `{serial}` | Chassis serial number |

## JSON output

```jsonc
{
    "type": "chassis",
    "result": {
        "type": "Laptop",
        "vendor": "LENOVO",
        "version": "None",
        "serial": "0123456789ABCDEF"
    }
}
```

## Examples

```jsonc
{ "type": "chassis", "format": "{type}" }
```

```jsonc
{ "type": "chassis", "key": "Form factor", "format": "{type} ({vendor})" }
```

## Pitfalls

- **An unmapped firmware type behaves differently per platform.** The SMBIOS type number is
  translated through a table covering 0x01–0x24. On Windows and the other SMBIOS readers an
  unmapped number leaves the field empty, so the module reports
  `chassis_type is not set by O.E.M.` even though the firmware did set a value. On Linux the
  raw sysfs contents are kept instead, so you get the bare number — for example `Chassis: 37`.
- **`{type}` is a translated string, not the SMBIOS number.** If you need the raw number for
  scripting, use `--format json` on a Linux box where the mapping failed, or read
  `/sys/class/dmi/id/chassis_type` yourself.
- **On Apple Silicon the type is guessed from the marketing name**, not read from firmware:
  anything starting with `MacBook ` is `Laptop`, `Mac mini ` / `Mac Studio ` are `Mini PC`,
  `iMac ` is `All in One`, and everything else — including Mac Pro — is `Desktop`. `{vendor}`
  is the host vendor (`Apple Inc.`), and `{version}` / `{serial}` are always empty.
- **FreeBSD returns a display string already.** Its `smbios.chassis.type` OID is documented in
  the source as returning the human-readable type directly, so it is not run through the
  translation table.
- **The high bit of the type byte is masked off.** SMBIOS defines bit 7 of the chassis type as
  a "chassis lock present" flag, and the table only looks at the low seven bits, so a locked
  laptop still reports `Laptop`.
- **`{serial}` is empty on Linux for an unprivileged user**, for the same reason as the Board
  module: the kernel exposes the DMI serial files as root-only.

## Implementation

### The type table

`ffChassisTypeToString()` lives in the shared `chassis.c` and is a plain `switch` over
`type & 0b01111111`, covering SMBIOS 7.4.1 *System Enclosure or Chassis Types*: `Other`,
`Unknown`, `Desktop`, `Low Profile Desktop`, `Pizza Box`, `Mini Tower`, `Tower`, `Portable`,
`Laptop`, `Notebook`, `Hand Held`, `Docking Station`, `All in One`, `Sub Notebook`,
`Space-saving`, `Lunch Box`, `Main Server Chassis`, `Expansion Chassis`, `SubChassis`, `Bus
Expansion Chassis`, `Peripheral Chassis`, `RAID Chassis`, `Rack Mount Chassis`,
`Sealed-case PC`, `Multi-system chassis`, `Compact PCI`, `Advanced TCA`, `Blade`,
`Blade Enclosure`, `Tablet`, `Convertible`, `Detachable`, `IoT Gateway`, `Embedded PC`,
`Mini PC` and `Stick PC`. Anything else returns `nullptr`, and every caller has to cope with
that — which is where the Linux/Windows difference above comes from.

### Linux

The primary read is `/sys/class/dmi/id/chassis_type`, a **decimal number** in sysfs. It is
converted with `ffStrbufToUInt(&type, 9999)` and passed through the table; because the code
only overwrites the strbuf when the table returned something, an unmapped value survives as
the decimal text. The sibling files `chassis_serial`, `chassis_vendor` and `chassis_version`
are read alongside.

Two device-tree fallbacks follow when DMI is unavailable:

1. `/sys/firmware/devicetree/base/smbios/smbios/chassis/chassis-type` — a **big-endian**
   `uint32_t`, so it is passed through `FF_READ_BE()` before translation. This is the Asahi
   Linux path, which also reads `…/chassis/manufacturer` into `{vendor}`.
2. `/sys/firmware/devicetree/base/chassis-type` — a plain string, NUL-trimmed, whose first
   character is upper-cased so `desktop` reads as `Desktop`.

Note that this second fallback deliberately does *not* go through the table: it prints the
device-tree string as-is, so a device tree that says `convertible` shows `Convertible` while a
DMI number of 31 shows `Convertible` too — same result, different route.

### Windows, Solaris, Haiku, OpenBSD, Intel macOS

SMBIOS type 3 is walked as a packed `FFSmbiosSystemEnclosure`, with a `static_assert` pinning
`ContainedElements` at offset 0x15. `Manufacturer`, `SerialNumber` and `Version` are resolved
with `ffSmbiosLocateString()` and cleaned with `ffCleanUpSmbiosValue()`; `Type` is a single
byte passed straight to `ffChassisTypeToString()`.

That last call is unguarded — unlike the Linux implementation, which checks for `nullptr` — but
it is not a crash: `ffStrbufSetStatic()` treats a `nullptr` as the empty string, so an unmapped
type quietly becomes empty rather than aborting.

### macOS (Apple Silicon)

`chassis_apple.c` delegates entirely to `ffDetectHost()`: it fills an `FFHostResult`, maps the
*product name* onto one of four types as described in the Pitfalls, copies the host vendor into
`{vendor}` and destroys the temporary host result. There is no firmware chassis type to read,
which is why `{version}` and `{serial}` are always empty on Apple Silicon.

### FreeBSD, NetBSD

FreeBSD reads the four `smbios.chassis.{type,maker,serial,version}` `kenv` OIDs. NetBSD reads
`machdep.dmi.chassis-{type,version,vendor,serial}` and cleans each field only when the lookup
succeeded.
