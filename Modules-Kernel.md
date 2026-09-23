# Kernel

> Print system kernel version

| | |
|---|---|
| Module type | `kernel` |
| Default order | 9 (only used by `--gen-config`) |
| Module source | `src/modules/kernel/kernel.c` |
| Detection source | — (no detection layer) |

Prints the kernel name and release, which is exactly what `uname` reports as `sysname` and
`release`.

```
Kernel: Linux 6.12.4-arch1-1
Kernel: Darwin 24.3.0
```

## Platform support

This module has **no detection layer**. It reads the `FFPlatformSysinfo` structure that
`FFPlatform` fills once at startup, so it is available on every platform fastfetch supports and
has no per-platform implementation to describe. That is why its row in the platform matrix is
all dashes.

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

Run `fastfetch -h kernel-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{sysname}` | Kernel name (`Linux`, `Darwin`, …) |
| `{release}` | Kernel release |
| `{version}` | Full build string — very long |
| `{arch}` | Machine architecture |
| `{page-size}` | Memory page size, as a formatted size |

## JSON output

```jsonc
{
    "type": "kernel",
    "result": {
        "architecture": "x86_64",
        "name": "Linux",
        "release": "6.12.4-arch1-1",
        "version": "#1 SMP PREEMPT_DYNAMIC Fri, 20 Dec 2024 14:30:00 +0000",
        "pageSize": 4096
    }
}
```

## Examples

```jsonc
{ "type": "kernel", "format": "{release}" }
```

```jsonc
{ "type": "kernel", "key": "Kernel", "format": "{sysname} {release} ({arch})" }
```

## Pitfalls

- **`{page-size}` is not a number.** It is rendered through the shared size formatter, so it
  follows `display.size`: with the default `iec` it prints `16.00 KiB`, with `si` it prints
  `16 kB`, and `display.size.ndigits` changes the decimals. The JSON output carries the raw
  byte count instead (`"pageSize": 16384`), so console and JSON are not interchangeable here.
- **`{version}` is the entire `uname -v` string**, which includes the build host, the toolchain
  and timestamps. It is several times longer than the rest of the line and is normally left out
  of a default config.
- **`{sysname}` here and `{sysname}` in the OS module are the same value.** Both read
  `instance.state.platform.sysinfo.name`, so a format that prints both always duplicates it.
  The kernel module calls it `sysname`; the same field is what the OS module defaults to when
  the distribution did not report a name.
- **The default line has no label distinguishing kernel from OS.** `Kernel: Darwin 24.3.0`
  alongside `OS: macOS Sequoia 15.3` is easy to misread, since on macOS the kernel name is
  `Darwin` while the OS name is `macOS`.
- **Nothing is configurable beyond the shared keys** — there is no way to ask for a specific
  `uname` field beyond what the format variables expose.

## Implementation

### Everything at once

`ffPrintKernel()` is short. It takes `const FFPlatformSysinfo* info =
&instance.state.platform.sysinfo` — a structure filled once during startup by the `FFPlatform`
detection code, not by this module — and then either prints `name release`, or formats the
template with five arguments.

`ffGenerateKernelJsonResult()` reads the same structure and writes `architecture`, `name`,
`release`, `version` and `pageSize` (as a `uint64_t`).

The one piece of arithmetic is the page size: `FFPlatformSysinfo` stores a *shift*, not a
byte count, so both the console and the JSON writer compute `(uint64_t) 1 << pageSizeShift`.
The console formatter then feeds that number to `ffSizeAppendNum()`, which reads
`instance.config.display.sizeBinaryPrefix` and picks the unit table accordingly — IEC
(`KiB`/`MiB`, base 1024), SI (`kB`/`MB`, base 1000), JEDEC (`KB`/`MB`, base 1024) or a
bytes-only fallback for `none`.

### Why the module has no detection layer

Most modules split into a UI layer under `src/modules/<name>/` and a platform layer under
`src/detection/<name>/`. Kernel does not, because `uname(2)` is POSIX and `FFPlatform` already
calls it for the OS, host, shell and terminal modules. Adding a detection layer would only
duplicate work the process has already done. The same applies to Title, Separator, Custom,
Break, Colors and DateTime.

### Keeping the two argument lists in sync

The `formatArgs` list and the argument array in `ffPrintKernel()` are maintained separately, and
nothing enforces that they agree. They currently do — the same five names on each side:

| Declared in `formatArgs` | Passed to the formatter |
|---|---|
| `sysname`, `release`, `version`, `arch`, `page-size` | `sysname`, `release`, `version`, `arch`, `page-size` |

`formatArgs` is what `-h kernel-format` reads, while the argument array is what actually resolves a
placeholder. A name that is declared but not passed is emitted verbatim rather than reported, so a
drift between the two lists is silent unless you print the variable. The module used to advertise a
`{display-version}` that printed as literal text for exactly that reason.
