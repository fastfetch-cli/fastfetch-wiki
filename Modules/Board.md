# Board

> Print motherboard name and other information

| | |
|---|---|
| Module type | `board` |
| Default order | 7 (only used by `--gen-config`) |
| Module source | `src/modules/board/board.c` |
| Detection source | `src/detection/board/` |

Prints the motherboard or baseboard product name, with its version in parentheses when the
firmware reports one.

```
Board: B650M AORUS ELITE AX (x.x)
```

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `board_linux.c` | DMI sysfs, falling back to device-tree (`/sys/firmware/devicetree/base/`) |
| Android | `board_android.c` | Android system properties |
| FreeBSD / MidnightBSD / DragonFly | `board_bsd.c` | `kenv` |
| NetBSD | `board_nbsd.c` | `machdep.dmi` sysctls |
| macOS | `board_windows.c` on Intel, `board_apple.c` on arm64 | IOKit |
| Windows | `board_windows.c` | SMBIOS type 2 (Baseboard Information) |
| Solaris / illumos / Haiku | `board_windows.c` | SMBIOS type 2 as well |
| GNU/Hurd | `board_nosupport.c` | Reports `Not supported on this platform` |

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

Run `fastfetch -h board-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{name}` | Board name |
| `{vendor}` | Board vendor |
| `{version}` | Board version |
| `{serial}` | Board serial number |

## JSON output

```jsonc
{
    "type": "board",
    "result": {
        "name": "B650M AORUS ELITE AX",
        "vendor": "Gigabyte Technology Co., Ltd.",
        "version": "x.x",
        "serial": ""
    }
}
```

## Examples

```jsonc
{ "type": "board", "format": "{vendor} {name}" }
```

```jsonc
{ "type": "board", "key": "", "format": "{name}" }
```

## Pitfalls

- **`{serial}` is normally empty on Linux for an unprivileged user.** The kernel exposes
  `/sys/class/dmi/id/board_serial` with mode `0400`, so a normal user gets a read error and the
  field stays empty; the module reports that as an empty string rather than as an error.
  Running fastfetch as root fills it in.
- **An empty name aborts the module.** `board_name is not set.` — a white-box board that leaves
  the DMI placeholder `To be filled by O.E.M.` in place has its value cleaned away and the
  module prints nothing at all.
- **On Apple Silicon `{name}` is a board ID, not a product name.** `board_apple.c` prefers the
  IORegistry `board-id` property and only falls back to the registry entry's name, so the
  output is an opaque identifier unless you rename the key.
- **`{vendor}` is often `Apple Inc.` or the OEM, never the actual board maker**, on Macs and on
  many laptops where the ODM's name is not in the DMI table.
- **The Linux device-tree fallback can swap fields.** When only `/sys/firmware/devicetree/base/compatible`
  is readable, the string `vendor,model` is split on the comma: the part before it becomes
  `{vendor}` and the part after it becomes `{name}`.

## Implementation

### Linux

The primary path is DMI sysfs, read through `ffGetSmbiosValue()` with
`/sys/devices/virtual/dmi/id/` preferred and `/sys/class/dmi/id/` as the fallback:
`board_name` (the mandatory field), `board_serial`, `board_vendor` and `board_version`.

Three device-tree fallbacks follow, tried in order, and the first one that yields a name wins:

1. `/sys/firmware/devicetree/base/smbios/smbios/baseboard/product`, with `…/baseboard/manufacturer`
   read into `{vendor}` when present.
2. `/sys/firmware/devicetree/base/board`, trimmed of trailing whitespace.
3. `/sys/firmware/devicetree/base/compatible`, split at the first comma as described above.

Device-tree strings are NUL-terminated within a fixed-size cell, so every read is followed by
`ffStrbufTrimRight(…, '\0')` — without that the value would carry the padding.

### Windows, Intel macOS, Solaris, Haiku

`ffGetSmbiosHeaderTable()` is used to locate SMBIOS type 2, which is walked as a packed
`FFSmbiosBaseboard`. The four string fields (`Manufacturer`, `Product`, `Version`,
`SerialNumber`) are resolved with `ffSmbiosLocateString()` and passed through
`ffCleanUpSmbiosValue()`, which maps the usual firmware placeholders — `To be filled by O.E.M.`,
`To be set by O.E.M.`, `System Product Name`, `None`, … — to an empty string. A field whose
string index is `0` resolves to `nullptr`, which `ffStrbufSetStatic()` stores as an empty
string, so missing strings are not an error.

The struct is padded out to `ContainedObjectHandles` with a `static_assert` on offset `0x0F`;
that field and the following variable-length array are never read, but asserting the offset
catches any compiler that would pack the structure differently from the SMBIOS definition.

### macOS (Apple Silicon)

`IOServiceGetMatchingService(IOServiceMatching("IOPlatformExpertDevice"))` is opened once.
`{name}` comes from the `board-id` property, and only if that is missing from
`IORegistryEntryGetName()`. The IOKit `version` and `manufacturer` properties fill the other two
fields. There is no separate board serial on this path, so `{serial}` stays empty.

### Android, BSD

**Android** reads `ro.product.board` and falls back to `ro.board.platform`. It fills only
`{name}`, so `{vendor}`, `{version}` and `{serial}` are always empty.

**FreeBSD** uses the `kenv` OIDs `smbios.planar.product`, `.serial`, `.maker` and `.version` —
note that `serial` is populated here, unlike on Linux, and that every value goes through
`ffCleanUpSmbiosValue()`.

**NetBSD** reads the `machdep.dmi.board-{product,version,vendor,serial}` sysctls, cleaning each
value only when the lookup succeeded.
