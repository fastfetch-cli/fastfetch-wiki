# TPM

> Print information about the Trusted Platform Module (TPM) security device

| | |
|---|---|
| Module type | `tpm` |
| Default order | 70 (only used by `--gen-config`) |
| Module source | `src/modules/tpm/tpm.c` |
| Detection source | `src/detection/tpm/` |

Prints the version of the security device and how it is attached. The default output prefers the
description and falls back to the version.

```
TPM: HW TPM 2.0
TPM: 2.0
TPM: I/O-port or MMIO TPM 2.0
TPM: Apple Silicon Security
```

The description is built by the backend, so its wording differs per platform — on Windows it is
`<interface> TPM <version>`, on FreeBSD it is the kernel's own device description, and on macOS it
is a fixed string.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `tpm_linux.c` | `/sys/class/tpm/tpm0/` |
| FreeBSD | `tpm_bsd.c` | `sysctl dev.tpmcrb.0.%desc` |
| macOS | `tpm_apple.c` | Apple T2 (Intel) or the Secure Enclave (Apple Silicon) |
| Windows | `tpm_windows.c` | `TBS.dll` → `Tbsi_GetDeviceInfo()` |
| Android / NetBSD / OpenBSD / Solaris / Haiku / GNU/Hurd | `tpm_nosupport.c` | Stub: always reports `Not supported on this platform` |

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | `TPM` | Module key. A single space hides the key and the separator |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

There are no module-specific options.

## Format string

Run `fastfetch -h tpm-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{version}` | TPM device version |
| `{description}` | TPM general description |

Neither is marked `*` in the help output, so neither is available in the key format.

`{version}` is `1.2`, `2.0` or `unknown`; it can also be empty on Linux, where the version file is
optional. `{description}` is empty when the backend could not name the interface.

## JSON output

```jsonc
[
    {
        "type": "TPM",
        "result": {
            "version": "2.0",
            "description": "HW TPM 2.0"
        }
    }
]
```

- Both keys are always present, as strings; an unknown value is `""`, never `null`.
- On failure the object carries `error` instead and has no `result`.
- The key order is `version`, `description` — the reverse of the order the default output prefers
  them in.

## Examples

```jsonc
// Version only, for a one-line summary
{ "type": "tpm", "format": "TPM {version}" }
```

```jsonc
// Hide the module when the version could not be read
{ "type": "tpm", "format": "{?version}TPM {version}{?}" }
```

## Pitfalls

- **The default output prefers the description, not the version.** `description` is printed when it
  is non-empty and `version` only otherwise, so a machine where the backend names the interface
  prints `HW TPM 2.0` rather than `2.0`. Use `{version}` in a format if you want the number alone.
- **A successful detection can print nothing on Linux.** `tpm_linux.c` returns success as soon as
  `/sys/class/tpm/tpm0/` exists, even if neither `tpm_version_major` nor `device/description` could
  be read. Both fields are then empty and the default output is a bare `TPM:`. This happens on
  kernels that expose the directory without the version file.
- **Only `tpm0` is inspected on Linux.** The path is hard-coded, so a second TPM (or a device that
  enumerated as `tpm1`) is invisible.
- **The Linux version is read from a single-character file.** `tpm_version_major` contains `1` or
  `2`; only `2` is expanded to `2.0`, and anything else is printed as-is (a `1` prints as `1`, not
  `1.2`). TPM 1.2 therefore usually shows an empty version, because the file does not exist on
  older kernels.
- **The FreeBSD version is guessed from the description string.** `dev.tpmcrb.0.%desc` is searched
  for the substrings `2.0` and `1.2`; when neither is present the version is the literal `unknown`.
- **On macOS the answer is not a TPM.** An Apple Silicon Mac always reports
  `2.0` / `Apple Silicon Security` — the Secure Enclave, which the module treats as equivalent. An
  Intel Mac reports `2.0` / `Apple T2 Security Chip` when the `AppleT2` IOKit service exists, and
  otherwise fails with `No Apple Security hardware detected`.
- **Windows reports `unknown` for an unrecognised version.** The version switch covers
  `TPM_VERSION_12` and `TPM_VERSION_20`; anything else becomes `unknown`, and the description then
  reads e.g. `HW TPM unknown`.
- **The Windows description is empty for an unrecognised interface.** Only `TPM_IFTYPE_1`
  (`I/O-port or MMIO`), `TRUSTZONE`, `HW`, `EMULATOR` (`SW-emulator`) and `SPB attached` produce a
  description, so a firmware with a different interface type falls back to printing the version.
- **`TBS.dll` is loaded dynamically.** When the library or `Tbsi_GetDeviceInfo` is missing the
  module fails with a load error rather than reporting "no TPM"; a genuine absence of the device is
  reported as `TPM device is not found` from `TBS_E_TPM_NOT_FOUND`.
- **Unsupported platforms still register the module.** The `_nosupport` stub returns
  `Not supported on this platform`, so on Android, NetBSD, OpenBSD, Solaris, Haiku and GNU/Hurd the
  module is present but always errors — silently, unless `display.showErrors` is `true`.
- **No TPM state other than the version and the interface is reported.** Enabled / disabled,
  owned, and the firmware version are not exposed, even where the platform API could supply them.

## Implementation

`ffDetectTPM()` fills an `FFTPMResult` — two `FFstrbuf`s, `version` and `description` — and returns
an error string or `nullptr`. `ffPrintTPM()` prints the description when it is non-empty and the
version otherwise; `ffGenerateTPMJsonResult()` writes both keys.

### Linux

Two existence checks on `/sys/class/tpm/` produce the two distinct errors
(`TPM is not supported by kernel` when the class directory itself is missing, `TPM device is not
found` when only `tpm0` is). The version comes from `/sys/class/tpm/tpm0/tpm_version_major` and the
description from `/sys/class/tpm/tpm0/device/description`; both are trimmed of trailing whitespace
and both reads are optional.

### FreeBSD

`ffSysctlGetString("dev.tpmcrb.0.%desc", …)` is the primary probe. When it fails, `ffKmodLoaded("tpm")`
distinguishes "the driver is not loaded" from "no device". On success the version is derived from
the description substring.

### macOS

The aarch64 build is a compile-time branch that always reports the Secure Enclave. The other
branch looks up `IOServiceMatching("AppleT2")` with `IOServiceGetMatchingService()`; the service is
released automatically. Nothing else is queried, so a Mac without either reports an error.

### Windows

`FF_LIBRARY_LOAD_MESSAGE(tbs, "TBS" …)` loads the TPM Base Services library at runtime and resolves
`Tbsi_GetDeviceInfo`. The returned `TPM_DEVICE_INFO` supplies `tpmVersion` (mapped to `1.2` / `2.0`
/ `unknown`) and `tpmInterfaceType` (mapped to the description prefix), and the version is embedded
in the description with `ffStrbufSetF()`.
