# Bootmgr

> Print second-stage bootloader information (name, firmware, etc.)

| | |
|---|---|
| Module type | `bootmgr` |
| Default order | 6 (only used by `--gen-config`) |
| Module source | `src/modules/bootmgr/bootmgr.c` |
| Detection source | `src/detection/bootmgr/` |

Prints the currently selected boot entry and the firmware file it loads.

```
Boot Manager: Windows Boot Manager - \EFI\Microsoft\Boot\bootmgfw.efi
```

Note the naming: the module's `type` is `bootmgr` and its `.name` — which is what the JSON
`type` field reports — is `Bootmgr`, but the printed key is the display name **`Boot Manager`**.
`"Bootmgr"` in a config is accepted; `"Boot Manager"` is not.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `bootmgr_linux.c` | UEFI variables in `/sys/firmware/efi/efivars/` |
| macOS | `bootmgr_apple.c` | IOKit device tree |
| Windows | `bootmgr_windows.c` | `NtQuerySystemEnvironmentValueEx` + `SeSystemEnvironmentPrivilege` |
| Haiku | `bootmgr_haiku.cpp` | Hard-coded Haiku loader path |
| FreeBSD / NetBSD / OpenBSD | `bootmgr_bsd.c` | `kenv`/`efi` properties |
| Android / Solaris / GNU/Hurd | `bootmgr_nosupport.c` | Reports `Not supported on this platform` |

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

Run `fastfetch -h bootmgr-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{name}` | Name / description of the boot entry |
| `{firmware-path}` | Full path of the firmware file |
| `{firmware-name}` | File name part of the firmware path |
| `{secure-boot}` | `true` / `false` |
| `{order}` | Boot order index of the selected entry |

## JSON output

```jsonc
{
    "type": "Bootmgr",
    "result": {
        "name": "Windows Boot Manager",
        "firmware": "\\EFI\\Microsoft\\Boot\\bootmgfw.efi",
        "order": 1,
        "secureBoot": true
    }
}
```

## Examples

```jsonc
{ "type": "bootmgr", "format": "{name}" }
```

```jsonc
{ "type": "bootmgr", "key": "Boot", "format": "{name} ({firmware-name}) secure={secure-boot}" }
```

## Pitfalls

- **`{secure-boot}` cannot distinguish "disabled" from "unknown".** The result field starts as
  `false` and is only overwritten when the platform can actually read the setting. On Linux that
  means the `SecureBoot` EFI variable was readable, and on macOS that an IOKit property
  existed; when either probe fails the module silently prints `false`.
- **Nothing is printed on a legacy-boot machine.** The Linux reader needs
  `/sys/firmware/efi/efivars/BootCurrent-<GUID>`; without it the module reports
  `Failed to read efivar: BootCurrent` rather than falling back to the MBR/BIOS bootloader.
- **Windows needs administrator rights.** The reader enables `SeSystemEnvironmentPrivilege`, and
  when the token lacks it the error is literally
  `The token does not have the specified privilege; try sudo please`.
- **macOS usually reports an empty `{firmware-path}`.** The path is only filled in when
  `/System/Library/CoreServices/boot.efi` exists, which is not the case on Apple Silicon, so
  `- ` (with nothing after the dash) is not printed and `{firmware-name}` is empty.
- **`{firmware-name}` only understands UEFI paths.** For every platform except Apple it splits
  on the last `\`, matching the UEFI File Path Media Device Path convention. Haiku hard-codes a
  `/`-separated path (`/system/haiku_loader.bios_ia32`), so on Haiku `{firmware-name}` returns
  the whole path.
- **`{order}` is a boot entry number, not a list.** It is the `####` of `BootCurrent`, i.e. the
  index of the entry that was booted — not the firmware's boot order.
- **The macOS name is truncated at the first `-`.** `mBoot-9.9.9` becomes `mBoot`.

## Implementation

### Linux

Reads `/sys/firmware/efi/efivars/BootCurrent-<EFI_GLOBAL_GUID>` into a 2048-byte aligned
buffer and expects exactly 6 bytes; the firmware-reported value is the 16-bit little-endian
`uint16_t` at offset 4 (the first four bytes are the EFI variable attributes). That number is
formatted as `Boot%04X` and read back, together with the `SecureBoot` variable, whose byte 4 is
`1` when secure boot is enabled.

The `Boot####` payload is decoded by the shared `ffEfiFillLoadOption()`: the variable's first
four bytes are skipped, then the structure is read as `FFEfiLoadOption`
(`Attributes`, `FilePathListLength`, a UCS-2 `Description[]`). The description is converted to
UTF-8 by hand, and the device-path list is walked node by node using each node's own `Length`
until the end-of-list type `0x7F`; the first File Path Media Device Path (type 4, subtype 4) is
decoded into `{firmware-path}`. A payload that is not at least
`5 + sizeof(FFEfiLoadOption)` bytes long, or that exactly fills the 2048-byte buffer, is
rejected as `Failed to read efivar: Boot####` — the second condition is how the code detects
that the variable was longer than the buffer.

### Windows

`enablePrivilege(L"SeSystemEnvironmentPrivilege")` is called first, through
`NtOpenProcessToken` / `NtAdjustPrivilegesToken`. A `STATUS_NOT_ALL_ASSIGNED` result is reported
as a privileges error rather than as a generic failure.

The firmware variables are then read with `NtQuerySystemEnvironmentValueEx`, using
`RtlGUIDFromString` on `{8be4df61-…}`. Both calls validate the returned size: `BootCurrent` must
return exactly `sizeof(uint16_t)`, and `Boot####` must neither be shorter than
`sizeof(FFEfiLoadOption)` nor fill the 2048-byte buffer exactly. The payload is decoded by the
same `ffEfiFillLoadOption()` as on Linux, so the two platforms agree on how a UEFI boot entry is
parsed. Secure boot comes from `SystemSecureBootInformation`, which is a direct query rather
than an EFI variable, so it does not depend on the privilege above.

### macOS

The reader is deliberately minimal: `/System/Library/CoreServices/boot.efi` is recorded as
`{firmware-path}` when the file exists, and the bootloader name comes from
`IODeviceTree:/chosen`'s `iboot-stage-two-tag`, falling back to `system-firmware-version`. The
name is cut at the first `-` and defaults to `iBoot`.

Secure boot is probed differently per architecture. On `__aarch64__` the `secure-boot` property
of `IODeviceTree:/chosen` is read as a single byte. On Intel the
`AppleSecureBootPolicy` property of `IODeviceTree:/options` is read instead, and "secure" means
any value other than `0x02` (Permissive Security). Both paths validate the property's Core
Foundation type and length before dereferencing, and both return an error string that the
caller discards only after assigning to the result — a probe failure leaves `false`.
