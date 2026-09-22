# OS

> Print the OS or Linux distribution name and version

| | |
|---|---|
| Module type | `os` |
| Default order | 3 (only used by `--gen-config`) |
| Module source | `src/modules/os/os.c` |
| Detection source | `src/detection/os/` |

Prints one line: the distribution's pretty name, with the architecture appended when the
pretty name does not already mention it.

The key defaults to the module name and is itself a format string when you override it; it
may use `{sysname}`, `{name}`, `{icon}` and `{module-name}`.

```
OS: Ubuntu 24.04.1 LTS (Noble Numbat) x86_64
OS: macOS Sequoia 15.3 (24D60) arm64
OS: Windows 11 Pro (24H2) x86_64
```

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `os_linux.c` | `/etc/os-release`, with distribution-specific fixups |
| Android | `os_android.c` | Android system properties (`ro.build.version.*`) |
| FreeBSD / MidnightBSD / DragonFly | `os_linux.c` | Same `os-release` reader as Linux |
| NetBSD | `os_nbsd.c` | Name is hard-coded, version from `uname -r` |
| OpenBSD | `os_obsd.c` | Name is hard-coded, version from `uname -r` |
| Solaris / illumos | `os_sunos.c` | First line of `/etc/release` |
| Haiku | `os_haiku.c` | Parses the API version of the loaded `libroot` image |
| GNU/Hurd | `os_linux.c` | Same `os-release` reader as Linux |
| macOS | `os_apple.m` | `SystemVersion.plist`, plus a hard-coded codename table |
| Windows | `os_windows.c` | `winbrand.dll` `BrandingFormatString` |

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

There are no module-specific keys — everything the module can print is reachable through
`format`.

## Format string

Run `fastfetch -h os-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{sysname}` | Kernel name, e.g. `Linux`, `Darwin` |
| `{name}` | OS name as recorded by the distribution |
| `{pretty-name}` | Distribution's preferred display name |
| `{id}` | Lower-case distribution ID, e.g. `ubuntu`, `macos` |
| `{id-like}` | Parent distribution, e.g. `debian` |
| `{variant}` | Variant string |
| `{variant-id}` | Variant ID |
| `{version}` | Version string (may contain the codename) |
| `{version-id}` | Version ID |
| `{codename}` | Version codename |
| `{build-id}` | Build ID |
| `{arch}` | Machine architecture |
| `{kernel-release}` | Release of the kernel, e.g. `27.0.0` |

## JSON output

```jsonc
{
    "type": "os",
    "result": {
        "buildID": "24D60",
        "codename": "Sequoia",
        "id": "macos",
        "idLike": "",
        "name": "macOS",
        "prettyName": "macOS Sequoia 15.3 (24D60)",
        "variant": "",
        "variantID": "",
        "version": "15.3",
        "versionID": "15.3"
    }
}
```

## Examples

```jsonc
{ "type": "os", "key": "{icon} {name}", "format": "{pretty-name} ({id})" }
```

```jsonc
{ "type": "os", "format": "{name} {kernel-release} {arch}" }
```

## Pitfalls

- **`{kernel-release}` is the raw kernel release, not a distribution version.** It is the
  `uname -r` value (`27.0.0`, `6.12.9-arch1-1`), which is what makes it useful for a
  distro-agnostic format — it says nothing about the distribution, unlike `{version}`.
- **The printed architecture is not in the JSON output.** The console line appends `{arch}`,
  but `ffGenerateOSJsonResult` writes only the ten distribution fields — a config that reads
  `--format json` gets a different field set than the console shows.
- **The architecture suffix is a substring test.** It is appended only when the built line does
  not already contain the architecture string, case-insensitively. A pretty name that happens to
  embed it (for example an OS whose name contains `aarch64`) suppresses the suffix.
- **On Ubuntu the distribution's own pretty name is discarded.** fastfetch rebuilds it as
  `NAME` + `VERSION` from `os-release` (which contains the codename, so it reads
  `Ubuntu 24.04.1 LTS (Noble Numbat)`). Editing `PRETTY_NAME` in `/etc/os-release` has no
  effect on the Ubuntu family.
- **`id-like` is empty on macOS and Android.** Neither implementation fills it; only the Linux
  (and Solaris) readers do.
- **Some values only exist on one platform.** `{build-id}` is empty on Linux, `{codename}` is
  empty on Windows unless the registry provides `DisplayVersion`, and `{id-like}` is empty on
  macOS.
- **The module fails loudly when nothing was found** — `Could not detect OS` means all of
  `name`, `pretty-name` and `id` were empty.
- **Detection has no runtime override.** `FASTFETCH_TARGET_DIR_ETC` is a *build-time* CMake
  value, not a command-line option; there is no `--target`.

## Implementation

### Linux, FreeBSD, DragonFly, GNU/Hurd

`detectOS()` runs a fallback chain, stopping as soon as enough fields are filled:

1. `/bedrock/strata/bedrock/etc/os-release` (Bedrock Linux only, skipped when
   `BEDROCK_RESTRICT=1`).
2. `<ETC>/os-release` — 11 keys, one `ffParsePropFileValues` pass: `PRETTY_NAME`, `NAME`, `ID`,
   `ID_LIKE`, `VARIANT`, `VARIANT_ID`, `VERSION`, `VERSION_ID`, `VERSION_CODENAME`, `CODENAME`,
   `BUILD_ID`. `PRETTY_NAME` is matched with a trailing space so `PRETTY_NAME` cannot
   accidentally match a longer key.
3. `<ETC>/lsb-release` — only when `os-release` left one of id/version/prettyName/codename
   empty; reads `DISTRIB_ID`, `DISTRIB_DESCRIPTION`, `DISTRIB_RELEASE`, `DISTRIB_CODENAME`.
4. `<USR>/lib/os-release` — only when id/name/prettyName are all still empty.
5. HarmonyOS, which ships no `os-release` file at all.

Because `os-release` keys are matched by *prefix*, a file that stores `DISTRIB_ID`-style keys
without the documented spacing still parses; the query strings include the `=` and the
surrounding space exactly as the file format specifies.

Distribution-specific post-processing then runs, keyed off `ID`:

- `ubuntu` — `getUbuntuFlavour()`. Armbian is recognised from its `PRETTY_NAME` prefix (and its
  ID is rewritten to `armbian` with `ID_LIKE` set to the original ID). Then Linux Lite, Rhino
  Linux and VanillaOS are matched by pretty-name prefix, LliureX by the presence of
  `/usr/bin/lliurex-version` (which it executes to get the version), and the official flavours —
  Ubuntu Studio, Kubuntu, Xubuntu, Lubuntu, Budgie, Cinnamon, MATE, Kylin, Sway, Touch, Unity —
  by comparing `$XDG_CONFIG_DIRS` against flavour keywords. **This one is environment
  dependent**: a KDE install whose `XDG_CONFIG_DIRS` does not mention `kde`/`plasma`/`kubuntu`
  is reported as plain Ubuntu.
- `debian` — `detectDebianDerived()`, matching Armbian, Loc-OS, Parrot Security, Lilidog,
  Proxmox VE (`/usr/bin/pveversion`), Proxmox Backup Server, Raspberry Pi OS (`/etc/rpi-issue`),
  DietPi (`/boot/dietpi/.version`, whose three components are appended to the pretty name),
  TrueNAS Scale (kernel release ends with `+truenas`), Emmabuntüs, and MX Linux (an
  `/etc/lsb-release` containing `DISTRIB_ID=MX`). Anything not matched falls through to
  `getDebianVersion()`, which replaces the version ID with `/etc/debian_version` and rebuilds
  the pretty name.
- `fedora` — CoreOS/Kinoite/Sericea/Silverblue append their variant ID to `ID`.
- `linuxmint` — `LMDE` is re-labelled as `lmde` / `id-like: linuxmint`.
- `deepin` — `/etc/os-version` supplies `MinorVersion` and `EditionName`.
- `astra` — the version is peeled off `VERSION_ID` (which looks like `2.12_x86-64`).

### macOS

`SystemVersion.plist` is parsed with `NSDictionary` for `ProductName`,
`ProductUserVisibleVersion` and `ProductBuildVersion`, falling back to the
`kern.osproductversion` / `kern.osversion` sysctls. The codename comes from a hard-coded
`major` → codename table (including a nested one for the 10.x releases) and is matched by the
*first* component of the version string. `prettyName` is assembled as
`name codename version (buildID)` and `id` is fixed to `macos`.

Two deliberate quirks:

- A version starting with `16.` has its first character rewritten to `2` — macOS 26 reported
  itself as `16.x` during development, and the codename table also accepts both 16 and 26.
- `id-like` is never set, so it is always empty on macOS.

### Windows

`BrandingFormatString(L"%WINDOWS_LONG%")` from `winbrand.dll` supplies the raw product string,
for example `Windows 11 Pro Insider Preview`. It is split into name / version / edition:

- `Windows ` and optional `Server ` / `Embedded ` prefixes become `name`.
- The first space-delimited token becomes `version`; a trailing `Rx` on a Server name is folded
  into the version.
- A leading `(TM) ` is stripped, and the `Microsoft ` prefix that WMI adds is removed so both
  sources agree.
- If the string does not start with `Windows `, the whole value is treated as the name.
- `getCodeName()` reads `DisplayVersion`, then `CSDVersion` (Windows 7/8), then `ReleaseId`
  (early Windows 10) from `SOFTWARE\Microsoft\Windows NT\CurrentVersion` and appends it to the
  pretty name in parentheses.
- `id` becomes `"<name> <version>"` (lower-cased later) and `id-like` is fixed to `Windows`.

`BrandingFormatString` allocates with `GlobalAlloc`, so the returned pointer is released with
`GlobalFree` after being copied into a `FFstrbuf`. There is deliberately no branding field for
the bare release number: `basebrd.dll` keeps it inside the same per-edition resource as
`%WINDOWS_LONG%`, so it can only be peeled off that string.

### Android, Haiku, Solaris, NetBSD, OpenBSD

- **Android** reads the `ro.build.version.release`, `ro.build.version.codename` and
  `ro.build.id` system properties. The codename has a built-in version→name table (the same
  desert names as the Android version history); the property is only consulted when the major
  version is not in the table.
- **Haiku** walks `get_next_image_info(B_SYSTEM_TEAM, …)` and decodes the `api_version` of
  `libroot` — the high 16 bits are the release, the remainder encodes alpha/beta/pre-release
  states (odd last bit means "pre"). Very old images map to `BEOS` / `BONE` / `DANO`. If no
  image carries a version, `get_system_info().kernel_version` is printed as a hex release.
- **Solaris** takes the first line of `/etc/release`, drops anything from the first `(` onward,
  and uses the first word as `id` with `id-like: sunos`.
- **NetBSD / OpenBSD** hard-code the name and copy `uname -r` into `version`; there is no
  codename, id or pretty name, so the fallback builder in `os.c` assembles the line.
