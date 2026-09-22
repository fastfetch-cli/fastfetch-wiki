# Loadavg

> Print system load averages

| | |
|---|---|
| Module type | `loadavg` |
| Default order | 12 (only used by `--gen-config`) |
| Module source | `src/modules/loadavg/loadavg.c` |
| Detection source | `src/detection/loadavg/` (plus `src/detection/cpu/` for the expanded layout) |

Prints the three standard load averages — over 1, 5 and 15 minutes. The default layout puts all
three on one line; `compact: false` prints one line per window, with a percentage and an optional
bar.

```
Load Average: 53.03, 20.15, 16.47
```

```
Load Average (1 min): 15.00 (150%)
Load Average (5 min): 16.41 (164%)
Load Average (15 min): 15.48 (155%)
```

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `cpu_linux.c`, `loadavg_linux.c` | `/proc/loadavg`, with a `sysinfo()` fallback |
| Android | `cpu_linux.c`, `loadavg_linux.c` | `/proc/loadavg` is not readable by apps; `sysinfo()` is the only path |
| FreeBSD / NetBSD / OpenBSD / DragonFly | `cpu_bsd.c`, `loadavg_bsd.c` | `sysctl({CTL_VM, VM_LOADAVG})` |
| macOS | `cpu_apple.c`, `loadavg_bsd.c` | Same `VM_LOADAVG` reader |
| Solaris / illumos | `cpu_sunos.c`, `loadavg_sunos.c` | `getloadavg()` |
| Haiku | `cpu_haiku.c` | `loadavg_sunos.c` or `loadavg_nosupport.c`, chosen at configure time (see below) |
| GNU/Hurd | `cpu_linux.c`, `loadavg_sunos.c` | `getloadavg()` |
| Windows | `cpu_windows.c`, `loadavg_nosupport.c` | **Not supported** — always prints an error |

Two platforms share `loadavg_sunos.c`, which is a one-line wrapper around the C library's
`getloadavg()`: Solaris and GNU/Hurd. On the Hurd the Hurd's own `/proc/loadavg` is not used — the
block compiles `loadavg_sunos.c` and `loadavg_linux.c` is not built at all there.

The Haiku row is decided by `check_function_exists(getloadavg HAVE_GETLOADAVG)` in the Haiku
block of `CMakeLists.txt` (~line 1293), with `libbsd` added to the required libraries: if the
function is present `loadavg_sunos.c` is compiled, otherwise `loadavg_nosupport.c`. Because this
is a configure-time check, it cannot be expressed in the platform matrix — the same Haiku build
tree can produce either binary.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `ndigits` | integer 0–9 | `2` | Digits after the decimal point in the compact layout. |
| `compact` | boolean | `true` | Print all three values on one line. |
| `percent` | object | `{ green: 50, yellow: 80, type: 0 }` | Colour thresholds and output style for the percentage. |
| `key` | string | module name | Module key. In the expanded layout this is itself a format string (see below). |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works, `""` prints none. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

`percent.type` accepts the same values as `display.percent.type`; `0` means "inherit the global
setting", `1` prints the number, `2` a multicolour bar, `3` both, and so on.

### The key as a format string in the expanded layout

When `compact` is `false` and a custom `key` is set, the key is parsed with four variables:

| Variable | Value |
|---|---|
| `{index}` | Window index, **0-based** (`0`, `1`, `2`) |
| `{duration}` | Window length in minutes (`1`, `5`, `15`) |
| `{icon}` | The module's `keyIcon` value |
| `{module-name}` | The localised module name (`Load Average`) |

```jsonc
{ "type": "loadavg", "compact": false, "key": "L{index}/{duration}" }
```

```
L0/1: 7.17 (72%)
L1/5: 12.67 (127%)
L2/15: 14.10 (141%)
```

Note that `{index}` starts at `0` while `{duration}` starts at `1`. When no custom `key` is set,
the module builds `<module name> (<duration> min)` itself.

## Format string

Run `fastfetch -h loadavg-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{loadavg1}` | Load average over 1 minute |
| `{loadavg2}` | Load average over 5 minutes |
| `{loadavg3}` | Load average over 15 minutes |

A `format` replaces the whole layout: neither `compact` nor `percent` has any effect on it, and
the three values are printed by the generic format engine, which does not honour `ndigits`.

## JSON output

```jsonc
{
    "type": "Loadavg",
    "result": [
        53.02978515625,
        20.1484375,
        16.47412109375
    ]
}
```

`result` is an **array of three doubles**, not an object — one of the few modules whose JSON
`result` is not a map. The raw values carry full `double` precision, so they are much longer than
the two-digit text output.

## Examples

```jsonc
// Percentage relative to the number of online cores, one line per window
{ "type": "loadavg", "compact": false, "percent": { "type": 3, "green": 40, "yellow": 70 } }
```

```jsonc
// A single line in a custom unit
{ "type": "loadavg", "format": "1m {loadavg1} | 5m {loadavg2} | 15m {loadavg3}" }
```

## Pitfalls

- **`ndigits` is limited to `0`–`9`.** Anything outside that range prints
  `Property 'ndigits' must be an integer between 0 and 9` and the layout falls back to the default of
  2, so both `"ndigits": 200` and `"ndigits": -1` are rejected instead of quietly producing a very
  long or truncated number. The parser and the schema's `minimum`/`maximum` agree on the range.
- **`ndigits` does not apply to a custom `format`.** `{loadavg1}` is formatted by the shared
  format engine, which has its own precision — a config with `ndigits: 1` and
  `format: "{loadavg1}"` still prints `13.96`.
- **`percent` does nothing in the default (compact) layout.** The percentage is only computed in
  the `compact: false` branch, so a config that sets thresholds without turning `compact` off
  looks like it was ignored.
- **The percentage is not CPU utilisation.** It is `load / cpu.coresOnline × 100`, so a value
  above 100 means "more runnable tasks than cores", not "the CPU is over 100 % busy". On a
  10-core machine a load of 15 prints `(150%)`.
- **`compact: false` costs an extra CPU detection.** That branch calls `ffDetectCPU()` to learn
  `coresOnline`, which reads the CPU model, cache sizes and — on platforms that support it —
  frequencies. Use the compact layout if you only want the three numbers.
- **Windows always fails.** `loadavg_nosupport.c` returns `Not supported on this platform`, so
  the module prints an error line (or nothing at all when `display.showErrors` is `false`).
- **GNU/Hurd does not read the Hurd's own `/proc/loadavg`.** That block compiles
  `loadavg_sunos.c` and never builds `loadavg_linux.c`, so the Hurd reports whatever
  `getloadavg()` returns — the same source Solaris uses. A Hurd system whose libc cannot provide
  three samples reports `getloadavg() failed` instead. The `read(/proc/loadavg) failed` wording
  [Uptime](Modules/Uptime) uses for its own dead end therefore never appears on the Hurd any more.
- **Load average is a host-wide value.** Inside a container it reports the host's load, and in a
  container with a private, restricted `/proc` it may be missing entirely — the module then
  silently falls back to `sysinfo()`, which is not namespaced either.

## Implementation

### Linux and Android

`ffDetectLoadavg()` reads `/proc/loadavg` with `ffReadFileData()` (63-byte cap) and pulls the
first three whitespace-separated values out with `sscanf("%lf%lf%lf", …)`; it accepts the result
only when all three conversions succeed. The file is read rather than obtained from the `getloadavg()`
libc call on purpose — the comment cites issue #620 and container compatibility.

When that fails, Linux falls back to `sysinfo(&si)` and divides each `si.loads[i]` by
`1 << SI_LOAD_SHIFT`. Android skips the `/proc` read because the file is not readable by apps, so
`sysinfo()` is its only path. GNU/Hurd no longer compiles this file at all.

`ndigits` is parsed with the shared `ffJsonConfigParseUInt32()` helper, bounded to `9`, which is what
makes an out-of-range value an error instead of a `(uint8_t)` truncation.

### FreeBSD, NetBSD, OpenBSD, DragonFly and macOS

`sysctl({CTL_VM, VM_LOADAVG})` fills a `struct loadavg`, and each entry is divided by the
kernel-supplied `fscale` rather than by a fixed constant, so the result stays correct on
platforms that scale the raw counters differently.

### Solaris, GNU/Hurd, and (on Haiku) the `libbsd` path

`getloadavg(result, 3)` is used directly, with the `<sys/loadavg.h>` include guarded by
`__has_include` so a libc without that header still compiles against `<stdlib.h>`. Anything other
than exactly three samples is reported as `getloadavg() failed`. The same file serves Haiku
whenever `libbsd` provides the symbol.

### Windows

There is no implementation: `loadavg_nosupport.c` returns the error string
`Not supported on this platform`. The module is still registered, so it appears in
`--list-modules` and in the default structure on Windows, where it renders an error line.

### Shared

The three values are stored in a `double[3]` that the caller pre-fills with `0.0 / 0.0` (NaN), so
a detection that returns success without writing anything is visible in the output rather than
being reported as zeros. `ffPrintLoadavg()` and `ffGenerateLoadavgJsonResult()` each call
`ffDetectLoadavg()` themselves; a run uses one or the other, never both.
