# Swap

> Print swap (paging file) space usage

| | |
|---|---|
| Module type | `swap` |
| Default order | 41 (only used by `--gen-config`) |
| Module source | `src/modules/swap/swap.c` |
| Detection source | `src/detection/swap/` |

Prints swap space in use and the amount configured. By default every swap device is summed into one
line; `separate` gives each device its own line, and the device name is then appended to the key.

```
Swap: 1.23 GiB / 2.00 GiB (62%)
```

```
Swap (Encrypted): 1.23 GiB / 2.00 GiB (62%)
```

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `swap_linux.c` | `/proc/swaps` per device, falling back to `/proc/meminfo` |
| Android | `swap_linux.c` | Same file |
| GNU/Hurd | `swap_linux.c` | Same file |
| FreeBSD | `swap_bsd.c` | `sysctl vm.swap_info`, indexed until it fails |
| DragonFly BSD | `swap_bsd.c` | Same file, `sysctlbyname("vm.swap_info_array")` |
| NetBSD | `swap_obsd.c` | `swapctl(SWAP_STATS)` |
| OpenBSD | `swap_obsd.c` | Same file |
| Solaris / illumos | `swap_sunos.c` | `swapctl(SC_LIST)` |
| Haiku | `swap_haiku.c` | `get_system_info()` plus the `virtual_memory` driver settings |
| macOS | `swap_apple.c` | `sysctl vm.swapusage` |
| Windows | `swap_windows.c` | `NtQuerySystemInformation(SystemPagefileInformation)` |

All of them have a real implementation — there is no `swap_nosupport.c` in the tree.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `separate` | boolean | `false` | Print one line per swap device instead of a single summed line. |
| `percent` | object | `{ "green": 50, "yellow": 80, "type": 0 }` | Colour thresholds and style for the percentage. `type: 0` means "use `display.percentType`". |
| `key` | string | module name | Module key. A single space hides the key and the separator. A format string is accepted (see below). |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

`separate` changes the *console* output only; `--format json` always emits the per-device array, so
a JSON consumer sums the entries itself.

Unlike most modules, `key` is evaluated as a format string by this module, with `{index}`, `{name}`,
`{icon}` and `{module-name}` available. `{index}` is 1-based, and only `separate` mode gives it a
value.

## Format string

Run `fastfetch -h swap-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{used}` | Used size, formatted with `display.size` |
| `{total}` | Total size, formatted with `display.size` |
| `{percentage}` | Percentage used, as a number followed by `%` — no parentheses |
| `{percentage-bar}` | Percentage used, as a bar |
| `{name}` | Device name. Empty in the summed line |

In the summed line there is no device, so `{name}` renders as an empty string.

## JSON output

```jsonc
{
    "type": "Swap",
    "result": [
        {
            "name": "Encrypted",
            "used": 1325334528,
            "total": 2147483648
        }
    ]
}
```

`result` is always an array, with or without `separate`. `used` and `total` are raw bytes. A machine
with no swap reports `"result": []`. On failure the object is `{ "type": "Swap", "error": "…" }`.

## Examples

```jsonc
{ "type": "swap", "separate": true, "key": "{name}" }
```

```jsonc
{ "type": "swap", "format": "{used} / {total}" }
```

```jsonc
{ "type": "swap", "separate": true, "percent": { "green": 40, "yellow": 70 } }
```

## Pitfalls

- **`separate` does not affect `--format json`.** The JSON path always walks the device list, so the
  summed figure the default output shows has to be recomputed by the consumer. There is no
  `separate` field in the result either, so a consumer cannot tell which mode produced it.
- **`Disabled` and `Unused` mean different things, and JSON says neither.** The summed line prints
  `Disabled` when no swap device was reported at all, and `Unused` when devices exist but their
  combined total is zero. In JSON the first case is `"result": []` and the second is a list of
  zero-sized entries — there is no marker that distinguishes them from a working machine.
- **On macOS 26 and later a machine with swap turned off reports `Unused`, not `Disabled`.** The
  "is swap enabled" probe reads `vm.compressor_mode`, and that sysctl no longer exists on macOS 26,
  so the check is compiled out by an availability guard and the zero-sized entry is always emitted.
  On macOS 25 and earlier the same machine reports `Disabled` with an empty list.
- **The macOS device name is not a device.** It is `Encrypted` or `Normal`, derived from
  `xsw_usage.xsu_encrypted`. There is one entry on macOS, never one per swap file.
- **The Linux summed entry is called `Total`.** When `/proc/swaps` cannot be read — which is the
  normal case on Android — the module falls back to `/proc/meminfo` and creates a single entry named
  `Total`. `separate: true` therefore prints `Swap (Total)` on Android and `Swap (<path>)` on a
  desktop Linux.
- **On Linux a partially parsed `/proc/swaps` double counts.** The per-device parser appends entries
  as it goes and returns an error if a later line does not match, and the caller then appends the
  `/proc/meminfo` entry to the same list. A truncated or malformed `/proc/swaps` therefore yields
  the devices parsed so far *plus* a `Total` entry, and the summed line counts them twice. The read
  buffer is 32 KiB, so roughly 400 devices are needed to reach that state.
- **Two platforms cap the device count.** Solaris collects at most 8 devices
  (`FFMaxNSwap`) and DragonFly BSD at most 32 (a fixed `xswdev[32]`); anything beyond that is
  silently dropped from both the summed figure and the JSON array.
- **FreeBSD, DragonFly and OpenBSD skip entries they consider empty.** FreeBSD and DragonFly drop
  any device reporting zero blocks, and the OpenBSD/NetBSD path keeps only devices whose
  `SWF_ENABLE` flag is set. A device that exists but is not enabled is invisible rather than
  reported as zero.
- **`{percentage-bar}` is empty by default.** `display.percentType` defaults to `num | num-color`,
  which carries no bar bit, so the bar variable renders as an empty string until a bar is requested
  through `display.percentType` or through this module's `percent.type`.
- **`{name}` is empty unless `separate` is on**, so a format string built around it prints nothing
  in the default mode.

## Implementation

`ffPrintSwap()` calls `ffDetectSwap()` once and then either prints every entry or sums them into a
throwaway `FFSwapResult`. The summed entry keeps an empty name, which is why the key loses its
`(<name>)` suffix. Nothing is cached, so `--dynamic-interval` re-reads the system every round.

The percentage is computed by the module, not by the backend, and is guarded against a zero total.

### Linux, Android and GNU/Hurd

Two parsers, tried in order.

`/proc/swaps` is read and its header line skipped. Each remaining line is matched with
`sscanf("%255s %*[^\t]%llu %llu")`, which takes the path, skips the type column up to the next tab,
and reads the size and used columns in kibibytes. Paths are stored with spaces escaped as octal
sequences, so the module decodes any `\ooo` triple it finds before storing the name. A line that
does not match aborts the parse with `Invalid /proc/swaps format found`.

If that fails, `/proc/meminfo` is read instead and `SwapTotal` and `SwapFree` are turned into a
single entry named `Total`.

### FreeBSD and DragonFly BSD

FreeBSD resolves `vm.swap_info` to a MIB with `sysctlnametomib()` and then walks it with an
increasing index until `sysctl()` fails, which is how the kernel signals the end of the list.
DragonFly reads the whole `vm.swap_info_array` in one call into a fixed 32-element array.

Both share `addSwapEntry()`, which drops zero-block devices and names the rest `/dev/<devname>`, or
`[NFS]` when the device number is `NODEV`. Both check `xsw_version` against `XSWDEV_VERSION` and
report `xswdev version mismatch` rather than misreading a struct that changed layout.

### NetBSD and OpenBSD

`swapctl(SWAP_NSWAP)` gives the device count, the array is allocated for exactly that many entries,
and `swapctl(SWAP_STATS)` fills it. Only entries with `SWF_ENABLE` are kept. Sizes are in
`DEV_BSIZE` blocks, so the conversion is a plain multiply by 512.

### Solaris and illumos

A single `swapctl(SC_LIST)` call fills a `swaptbl_t` built in a local buffer sized for
`FFMaxNSwap` (8) entries, each pointing at its own `PATH_MAX` name buffer. The call returns the
number of entries actually filled. Used space is derived as `total − free`, both shifted by the
page-size shift.

### Haiku

`get_system_info()` supplies `max_swap_pages` and `free_swap_pages`. The name starts as `System`
and is replaced by `Auto` or `Manual` when the `virtual_memory` driver settings file can be loaded
and carries a `swap_auto` parameter.

### macOS

`sysctl(CTL_VM, VM_SWAPUSAGE)` fills an `xsw_usage`. When `xsu_total` is zero the module checks
whether swap is genuinely disabled: on macOS 26 and later that check is skipped, because
`vm.compressor_mode` no longer exists there, and on older releases a `vm.compressor_mode` value of
2 or less means "disabled" and produces an empty list.

### Windows

`NtQuerySystemInformation(SystemPagefileInformation)` fills a 4 KiB stack buffer and returns a
linked list of `SYSTEM_PAGEFILE_INFORMATION` entries, walked through `NextEntryOffset`. The name is
the pagefile path with a leading `\??\` removed; the size fields are page counts, so both are
shifted by the page-size shift.
