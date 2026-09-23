# Version

> Print the Fastfetch version and build information

| | |
|---|---|
| Module type | `version` |
| Default order | 71 (only used by `--gen-config`) |
| Module source | `src/modules/version/version.c` |
| Detection source | `src/detection/version/` (build constants), `src/detection/libc/` |

Prints the project name, the version, the commit-count tweak, the architecture, and — with a
format string — everything else that was baked in at compile time.

```
Version: fastfetch 2.69.0-104 (aarch64)
Version: fastfetch 2.69.0-103-debug (aarch64)
```

## Platform support

The module itself is platform independent. Only the libc field is detected per platform, and
that detection is entirely compile-time:

| Platform | Libc source | Reports |
|---|---|---|
| Linux | `libc_linux.c` | `glibc <major>.<minor>`, `uClibc <major>.<minor>.<patch>` or `musl <version>` |
| Android | `libc_android.c` | `ndk-bionic <major>.<minor>.<build>` when built with the NDK |
| FreeBSD / DragonFly | `libc_bsd.c` | `FBSD <version>` or `DF <version>` |
| GNU/Hurd | `libc_linux.c` | Same glibc branch as Linux |
| macOS | `libc_apple.c` | `libSystem <version>` |
| Windows | `libc_windows.cpp` | `ucrt <version>` or `msvcrt <version>` |
| NetBSD / OpenBSD / Solaris / Haiku | `libc_nosupport.c` | `Unknown`, with no version |

Every platform has a file, so the module has no "unsupported" state — the four platforms
listed with `libc_nosupport.c` still print something rather than failing.

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

There are no module-specific keys, and this is the one module that ignores
`general.detectVersion`: nothing here is detected at runtime, so there is nothing to disable.

## Format string

Run `fastfetch -h version-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{project-name}` | Project name (`fastfetch`) |
| `{version}` | Version without the tweak (`2.69.0`) |
| `{version-tweak}` | The commit-count suffix, including the leading dash (`-104`) |
| `{build-type}` | `debug` or `release`, derived from `NDEBUG` |
| `{sysname}` | Build target name (`Linux`, `macOS`, `FreeBSD`, …) |
| `{arch}` | Build target architecture (`aarch64`, `x86_64`, …) |
| `{cmake-built-type}` | `CMAKE_BUILD_TYPE` as passed to CMake (`Debug`, `Release`, …) |
| `{compile-time}` | `__DATE__` and `__TIME__` of the translation unit |
| `{compiler}` | Compiler name and version |
| `{libc}` | Libc name and version, from the table above |

## JSON output

```jsonc
{
    "type": "Version",
    "result": {
        "projectName": "fastfetch",
        "sysName": "macOS",
        "architecture": "aarch64",
        "version": "2.69.0",
        "versionGit": "2.68.1-104-gfe49a9624",
        "cmakeBuiltType": "Release",
        "compileTime": "Sep 22 2026, 11:30:18",
        "compiler": "Apple clang 21.0.0 (21000334)",
        "debugMode": false,
        "libc": "libSystem 1359.0.0"
    }
}
```

`libc` is `null` when the libc detection failed, which on this module only happens on Android
built without the NDK.

## Examples

```jsonc
{ "type": "version", "format": "{version}{version-tweak}" }
```

```jsonc
{ "type": "version", "key": "Built", "format": "{compiler} · {cmake-built-type}" }
```

## Pitfalls

- **The default output is not `{version}`.** It is
  `{project-name} {version}{version-tweak} {arch}`, with `-debug` inserted after the tweak in
  debug builds. So `2.69.0-104` is a version plus a tweak, not a four-component version, and a
  script that parses the line as `<name> <semver> (<arch>)` will mis-read it.
- **`{version-tweak}` includes its dash.** It is `-104`, not `104`. The field is empty when the
  build has no commit count, so `{version}{version-tweak}` degrades to the bare version.
- **`{version}` and `{versionGit}` disagree.** `{version}` is the release number from
  `CMakeLists.txt` (`2.69.0`); `versionGit` is `git describe` output (`2.68.1-104-gfe49a9624`)
  and can name an older release. The two are separate values and neither is derived from the
  other.
- **`versionGit` has no format variable.** It is present in the JSON result but is not in
  `-h version-format`, so `{version-git}` is printed literally. Read it from `--format json`
  if you need it.
- **`{libc}` describes the build toolchain, not the running system.** Every implementation
  reads preprocessor macros (`__GLIBC__`, `_UCRT`, `FF_LIBSYSTEM_VERSION`, …), so a binary
  copied to a different machine keeps reporting the libc it was compiled against.
- **`{libc}` is `Unknown` rather than empty on four platforms.** NetBSD, OpenBSD, Solaris and
  Haiku have no libc detection at all, and the stub reports success with the literal name
  `Unknown`. A format like `{libc} · {compiler}` therefore always has something in the first
  slot.
- **`{compiler}` reports the compiler that built this file.** On Windows with clang it reads
  `clang-cl <version>`; on Apple clang it appends the Apple build number in parentheses, so the
  string has a space and a `(...)` in it that naive parsing trips over.
- **`{compile-time}` is the build timestamp of `version.c`, not of the binary.** With
  incremental builds the module can report a time older than the linked executable.
- **`{arch}` and `{sysname}` are compile-time guesses.** They come from a hand-written
  `#if`-chain over compiler predefined macros, and anything unrecognised becomes `Unknown` —
  including architectures the chain has no branch for. They describe the build target, which is
  not necessarily the machine the binary runs on.

## Implementation

`src/detection/version/version.c` contains no detection code at all: it is one static
initializer for `ffVersionResult`, filled from the `FASTFETCH_PROJECT_*` macros that CMake
generates into `fastfetch_config.h`, plus `__DATE__ ", " __TIME__` for the timestamp and an
`#if`-chain for `sysName`, `architecture` and `compiler`.

`sysName` and `architecture` are resolved at compile time from predefined macros. The
architecture chain covers `x86_64`, `i386`, `ia64`, `aarch64`, `arm`, `mips`, `powerpc`,
`riscv`, `s390x`, `loongarch`, `sparc`, `alpha`, `hppa`, `sh` and `m68k`. The system chain
covers `Android`, `Linux`, `DragonFly`, `MidnightBSD`, `FreeBSD`, `macOS`, `Windows`, `SunOS`,
`OpenBSD`, `NetBSD`, `Haiku` and `GNU`; note that these are exactly the values accepted by
`condition.system`.

`debugMode` is `#ifndef NDEBUG`, so `{build-type}` follows the CMake build type only indirectly
— a `Release` build configured with `-UNDEBUG` would still print `debug`.

The module reads no options in `ffPrintVersion()`; it prints either the fixed default line or
the format string. The only work it does at runtime is the `{libc}` field, which calls
`ffDetectLibc()` and renders `<name> <version>` — the error return is ignored, so a failed
detection leaves the field empty rather than printing an error.
