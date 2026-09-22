# PhysicalMemory

> Print system physical memory devices

| | |
|---|---|
| Module type | `physicalmemory` |
| Default order | 40 (only used by `--gen-config`) |
| Module source | `src/modules/physicalmemory/physicalmemory.c` |
| Detection source | `src/detection/physicalmemory/` |

Prints one line per memory slot, describing the module installed in it. A populated slot shows the
size, the memory type, the rated speed, the configured speed when it differs, the vendor and an ECC
marker:

```
Physical Memory: 16.00 GiB - LPDDR5 (Samsung)
Physical Memory 1: 32.00 GiB - DDR5-4800 @ 4400 MT/s (Micron) - ECC
Physical Memory 2: Empty - DIMM (BANK0/DIMM1)
```

The rated speed is appended to the type with a hyphen and no unit (`DDR5-4800`); the configured
speed gets its own ` @ … MT/s` clause and only appears when it differs from the rated one. An
unpopulated slot, which is only shown when `showEmptySlots` is on, prints the word `Empty` plus the
form factor and the locator.

The key gains a 1-based index suffix only when more than one device is printed. Sizes are formatted
with the global `display.size` settings.

## Platform support

| Platform | Implementation | Data source |
|---|---|---|
| Linux | `physicalmemory_linux.c` | SMBIOS type 17, "Memory Device" |
| Android | `physicalmemory_nosupport.c` | **Not supported** |
| FreeBSD | `physicalmemory_linux.c` | SMBIOS |
| NetBSD | `physicalmemory_linux.c` | SMBIOS |
| OpenBSD | `physicalmemory_linux.c` | SMBIOS |
| Solaris / illumos | `physicalmemory_linux.c` | SMBIOS |
| Haiku | `physicalmemory_linux.c` | SMBIOS |
| GNU/Hurd | `physicalmemory_nosupport.c` | **Not supported** |
| macOS, Apple Silicon | `physicalmemory_apple.m` | IOKit `IODeviceTree:/chosen`, `system_profiler` as a fallback |
| macOS, Intel | `physicalmemory_linux.c` | SMBIOS |
| Windows | `physicalmemory_linux.c` | SMBIOS |

`CMakeLists.txt` picks the macOS file by architecture: `physicalmemory_linux.c` when
`CMAKE_SYSTEM_PROCESSOR` is `amd64`, and `physicalmemory_apple.m` otherwise. Android and GNU/Hurd
are the two platforms without an implementation and report `Not supported on this platform`.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `showEmptySlots` | boolean | `false` | Also list slots with no module installed. |
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

`showEmptySlots` is only meaningful for the SMBIOS-based implementations, because those are the only
ones that can see an empty slot at all.

## Format string

Run `fastfetch -h physicalmemory-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{bytes}` | Size in bytes |
| `{size}` | Size, formatted with `display.size` |
| `{max-speed}` | Rated speed in MT/s |
| `{running-speed}` | Configured speed in MT/s |
| `{type}` | Memory type (`DDR4`, `DDR5`, `LPDDR5`, …) |
| `{form-factor}` | Form factor (`DIMM`, `SODIMM`, `CAMM`, …) |
| `{locator}` | Bank and device locator, joined with `/` (`BANK0/DIMM1`) |
| `{vendor}` | Vendor |
| `{serial}` | Serial number |
| `{part-number}` | Part number |
| `{is-ecc-enabled}` | `true` when ECC is enabled |
| `{is-installed}` | `true` when a module is present |

Unlike the text output, the format variables do not compose the `-4800` suffix or the ` @ … MT/s`
clause for you — `{max-speed}` and `{running-speed}` are bare integers.

## JSON output

```jsonc
{
    "type": "PhysicalMemory",
    "result": [
        {
            "size": 17179869184,
            "installed": true,
            "maxSpeed": 0,
            "runningSpeed": 0,
            "type": "LPDDR5",
            "locator": "",
            "formFactor": "",
            "vendor": "Samsung",
            "serial": "",
            "partNumber": "",
            "ecc": false
        }
    ]
}
```

`result` is always an array, one object per slot, and every field is always present — unknown values
are empty strings or `0`, never `null`. `size` is in bytes and the two speeds are in MT/s. On
failure the object is `{ "type": "PhysicalMemory", "error": "…" }` with no `result` key.

## Examples

```jsonc
{ "type": "physicalmemory", "format": "{size} {type} ({vendor})" }
```

```jsonc
{ "type": "physicalmemory", "showEmptySlots": true }
```

```jsonc
{ "type": "physicalmemory", "format": "{locator}: {size} {part-number}" }
```

## Pitfalls

- **On Apple Silicon there is exactly one device, and most of its fields are empty.** The IOKit
  path reads `dram-type`, `dram-size` and `dram-vendor` from `IODeviceTree:/chosen`, which describe
  the soldered package as a whole. `formFactor`, `locator`, `serial` and `partNumber` are left
  empty, both speeds are `0` and `ecc` is `false`. Do not derive a slot count, a per-module size or
  an upgrade path from `result` on such a machine — the memory is not in slots.
- **`showEmptySlots` is a no-op on Apple Silicon.** The IOKit path always marks its single device as
  installed, so enabling the option changes nothing there.
- **A machine without SMBIOS reports an error rather than an empty list.** Observed in a Linux
  virtual machine whose `/sys/firmware/dmi/tables/` does not exist: the module returns
  `Failed to get SMBIOS data` and prints nothing. A consumer that treats "no devices" and "cannot
  read the firmware" as the same case will mis-report such a machine.
- **`ecc` is inferred from the bus widths.** The SMBIOS path sets it to
  `TotalWidth > DataWidth`, and only when both fields are present and neither is `0xFFFF`. A slot
  whose firmware reports `0xFFFF` for either width therefore reports `ecc: false` regardless of what
  the module actually is, and an empty slot always reports `false`.
- **`{size}` is empty for an empty slot while `{bytes}` is `0`.** The formatted size is only built
  when the slot is populated, so a format string that prints `{size}` silently loses the value for
  an empty slot where `{bytes}` would have shown `0`.
- **Using `format` adds a `1` to the key on a single-device machine.** The default text path passes
  an index of `0` — which suppresses the suffix — when there is only one device, but the format path
  always passes `i + 1`. A one-module machine therefore prints `Physical Memory:` with the default
  output and `Physical Memory 1:` as soon as a format string is configured.
- **A rated speed of `0` does not mean the slot is empty.** `maxSpeed` stays `0` whenever the
  firmware does not report one, which is the normal case on Apple Silicon and for soldered memory
  generally. Only `installed` and `size` say whether a module is present.
- **A slot can be `installed: true` with a size of `0`.** SMBIOS uses `0xFFFF` in the `Size` field
  to mean "size unknown", and the code leaves the size at `0` in that case instead of falling back
  to `ExtendedSize`. Such a slot prints `0 B - <type> (<vendor>)` and appears in JSON as
  `{ "installed": true, "size": 0 }` — which is a different thing from an empty slot, where
  `installed` is `false`.
- **The macOS fallback spawns `system_profiler`.** If the IOKit lookup fails, the module runs
  `system_profiler SPMemoryDataType -xml -detailLevel full` and parses its plist. That is a process
  launch plus a plist parse, so it is far slower than every other path and it re-runs on every
  round under `--dynamic-interval`.
- **`{serial}` and `{part-number}` are hardware identifiers.** The default output does not print
  them, but a format string can, and they are always present in `--format json`. Treat a captured
  fastfetch output as identifying if you enable them.
- **`{type}` can be the literal `Empty` or `Unknown (N)`.** On the SMBIOS path an unpopulated slot
  gets the type `Empty` even when `installed` is `false`, and a memory-type byte outside the known
  table is rendered as `Unknown (N)` with the raw number. Neither is a memory technology name.

## Implementation

`ffPrintPhysicalMemory()` and `ffGeneratePhysicalMemoryJsonResult()` both call
`ffDetectPhysicalMemory()` into a fresh list and free the six string buffers of every device
afterwards. Nothing is cached, so `--dynamic-interval` re-reads the firmware every round.

### Linux, the BSDs, Solaris, Haiku, Windows and Intel macOS

All of these share `physicalmemory_linux.c`, which walks SMBIOS type 17 records between the first
Memory Device entry and the end-of-table entry. Each record yields a device:

- `Size` is in MiB, or in KiB when bit 15 is set, and the `0x7FFF` sentinel means "use the 32-bit
  `ExtendedSize` field, in MiB". The `0xFFFF` sentinel — "unknown size" — leaves the size at `0`
  while still marking the slot as installed. A `Size` of `0` means the slot is unpopulated, and the
  record is skipped entirely unless `showEmptySlots` is on.
- `locator` is `BankLocator` and `DeviceLocator` joined with `/`, falling back to whichever of the
  two exists.
- `type` comes from a 38-entry table covering everything up to `MRDIMM`; `formFactor` from a
  20-entry table covering everything up to `CSODIMM`. Values outside a table become
  `Unknown (N)`.
- The strings — manufacturer, serial number, part number — are located through
  `ffSmbiosLocateString()` and then cleaned by `ffCleanUpSmbiosValue()`, which blanks the common
  firmware placeholders.
- `maxSpeed` is `Speed`, with `0xFFFF` meaning "read `ExtendedSpeed`"; `runningSpeed` is
  `ConfiguredMemorySpeed` with the same `0xFFFF` rule against `ExtendedConfiguredSpeed`. Both are
  read only when the record is long enough to contain the field, so an old SMBIOS revision simply
  leaves them at `0`.

The vendor string gets a second pass: `FFPhysicalMemoryUpdateVendorString()` recognises a bare
four-hex-digit JEDEC ID, or the same ID prefixed with `0x`, and replaces it with a name for thirteen
known manufacturers — Apacer, Kingston, Corsair, A-DATA, G-Skill, Crucial, Samsung, Transcend,
Micron, SK Hynix, Qimonda, Elpida and Ramaxel, covering eighteen IDs in total. The literal string
`Unknown` is cleared to empty first, and an ID that is not in the table is left as it was found.

### macOS

The Apple Silicon path is tried first and, when it succeeds, it is the only source used. It opens
`IODeviceTree:/chosen` and reads three properties: `dram-type` becomes `type`, `dram-vendor` becomes
`vendor` (and then goes through the same JEDEC ID mapping), and `dram-size` becomes `size`. All
three must be present or the path is abandoned.

The fallback runs `system_profiler SPMemoryDataType -xml -detailLevel full` and deserialises the
resulting plist. It handles two shapes: an `_items` array of per-slot dictionaries, which is what
Intel Macs produce, and a flat dictionary, which is what Apple Silicon produces. Sizes and speeds
arrive as strings such as `16 GB` and `4800 MHz`, so both are parsed with a unit suffix — `T`, `G`,
`M`, `K` — and converted to bytes and to MT/s respectively. Intel machines also supply the locator,
serial number, part number and an ECC flag derived from the plist's global ECC state.
