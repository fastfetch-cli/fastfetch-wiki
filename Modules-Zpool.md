# Zpool

> Print ZFS storage pools

| | |
|---|---|
| Module type | `zpool` |
| Default order | 44 (only used by `--gen-config`) |
| Module source | `src/modules/zpool/zpool.c` |
| Detection source | `src/detection/zpool/` |

Prints one line per ZFS pool, with the used and total size, the three percentages and the pool's
health. The key is `Zpool (<pool name>)`.

```
Zpool (tank): 1.20 TiB / 3.60 TiB (33%, 21% allocated, 5% frag) - ONLINE
Zpool (backup): 900.00 GiB / 1.80 TiB (49%, 49% allocated, 2% frag) - ONLINE [Read-only]
```

The percentages are, in order, the space used (`used / total`), the space allocated (`allocated /
total`) and the pool's fragmentation, which is a ZFS property rather than something the module
computes. `[Read-only]` is appended for a pool whose `readonly` property is set.

This module is a thin wrapper around **libzfs**, the same library `zpool(8)` uses. It is not a
reader of on-disk metadata: without the library, or without the `zfs` kernel module loaded, there is
nothing to print.

## Platform support

`src/detection/zpool/zpool.c` is platform-independent — it is listed in the shared part of
`CMakeLists.txt` and loads libzfs with `dlopen` at run time — so the module is built everywhere and
works wherever libzfs is installed. There is no per-platform backend and no `zpool_nosupport.c`.

| Platform | Implementation | Notes |
|---|---|---|
| All | `zpool.c` | libzfs through `dlopen`, behind `FF_HAVE_LIBZFS` |
| Solaris (not illumos) | `zpool.c` | `zpool_get_prop()` takes five arguments there instead of six; a macro drops the last one |

`ENABLE_LIBZFS` defaults to `ON` and defines `FF_HAVE_LIBZFS`. With it off, the whole file is
replaced by a stub that returns `fastfetch was compiled without libzfs support`.

## Configuration

The module has no keys of its own.

| Key | Type | Default | Description |
|---|---|---|---|
| `percent` | object | `green: 50`, `yellow: 80` | Colour thresholds for the percentages |
| `key` | string | `Zpool (<name>)` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

With `green <= yellow` the thresholds mean `[0, green]` green, `(green, yellow]` yellow and
`(yellow, 100]` red. `percent.type` defaults to `0`, which means "use `display.percent.type`".

## Format string

Run `fastfetch -h zpool-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{name}` | Pool name — also available in key format |
| `{guid}` | Pool guid — also available in key format |
| `{state}` | Pool health |
| `{size-used}` | Used space, formatted |
| `{size-allocated}` | Allocated space, formatted |
| `{size-total}` | Total space, formatted |
| `{used-percentage}` | Used space as a percentage |
| `{allocated-percentage}` | Allocated space as a percentage |
| `{frag-percentage}` | Fragmentation as a percentage |
| `{used-percentage-bar}` | Used space as a percentage bar |
| `{allocated-percentage-bar}` | Allocated space as a percentage bar |
| `{frag-percentage-bar}` | Fragmentation as a percentage bar |
| `{is-readonly}` | Whether the pool is read-only |

`{name}` and `{guid}` are the only two variables marked `*`, so the **key format has a different
vocabulary from the format string**: it receives `{index}`, `{name}`, `{guid}`, `{icon}` and
`{module-name}`, and none of the size or percentage variables. The default key is built from the pool
name rather than from the format string, which is why it is `Zpool (tank)` and not `Zpool`.

The three `…-percentage` variables are empty unless `percent.type` includes the `num` bit, and the
three `…-percentage-bar` variables are empty unless it includes `bar`. The default output is not
affected by that: it calls the percentage formatter directly and always prints the numbers.

## JSON output

```jsonc
{
    "type": "Zpool",
    "result": [
        {
            "name": "tank",
            "state": "ONLINE",
            "guid": 1234567890123456789,
            "used": 1319413953331,
            "allocated": 845000000000,
            "total": 3958687199232,
            "fragmentation": 5.0,
            "readOnly": false
        }
    ]
}
```

`used`, `allocated` and `total` are byte counts, `fragmentation` is a percentage and can be `null`,
and `guid` is an unsigned 64-bit integer. The computed percentages are **not** in the JSON result.
On failure the object is `{ "type": "Zpool", "error": "…" }`; a machine with no pool at all produces
`"result": []` in JSON while the text path reports `No zpool found`.

## Examples

```jsonc
// One pool per line, in a fixed shape
{ "type": "zpool", "format": "{name} {state} {size-used}/{size-total} ({used-percentage})" }
```

```jsonc
// A key that carries the pool name and the guid
{ "type": "zpool", "key": "Pool {name}" }
```

```jsonc
// Bars instead of numbers
{ "type": "zpool", "percent": { "type": ["bar", "num"] } }
```

## Pitfalls

- **libzfs is loaded at run time, not linked.** The module is compiled on every platform but the
  library is resolved with `dlopen` when the module runs, so a build on a machine without ZFS starts
  normally and fails only there, with the loader's own message —
  `dlopen(libzfs.so.6) failed` on Linux, `dlopen(libzfs.dll) failed` on Windows. "The module is
  available" and "the module can work" are two different things.
- **A missing kernel module is diagnosed separately.** When the library loads but `libzfs_init()`
  fails, the module asks whether the `zfs` kernel module is loaded and reports
  `` `zfs` kernel module is not loaded `` instead of a generic initialisation failure. A pool that
  exists but whose module has been unloaded therefore reads as a kernel problem, not as a library
  problem.
- **The JSON result has no percentages.** `usedPercentage` and `allocatedPercentage` are computed
  while printing, so a JSON consumer has to divide the byte counts itself and decide what to do when
  `total` is `0`.
- **`used` is derived, not read.** ZFS has no `used` property; the detection layer computes
  `total - free` from the `size` and `free` properties, so the value is only as good as those two.
- **`{guid}` is an unsigned 64-bit integer in a JSON number.** Most JSON consumers decode numbers as
  doubles, and a guid above 2^53 loses its low digits on the way in. A script that keys on the guid
  has to read the field as text.
- **`fragmentation` is the one field that can be `null`.** A pool that does not report the property
  yields the `-DBL_MAX` sentinel internally, which the JSON writer turns into `null` and the
  percentage formatter renders as a bare `-` with a bar made of the "total" character. The default
  line then reads `(-, 21% allocated, - frag)`.
- **The default line always prints the percentages, but the variables do not.** `percent.type: []`
  leaves `Zpool (tank): 1.20 TiB / 3.60 TiB (33%, 21% allocated, 5% frag) - ONLINE` unchanged while
  `{used-percentage}` becomes empty in a custom format.
- **Multiple pools are indexed after the key, not after the name.** With two pools the lines begin
  `Zpool (tank) 1:` and `Zpool (backup) 2:`; a custom key such as `Z[{name}]` renders as `Z[tank] 1:`.
  The index is not part of the JSON result.
- **`No zpool found` is an error, not an empty line.** A machine with a working libzfs and no pool
  prints nothing at all unless `display.showErrors` is `true`, and the JSON path answers with an
  empty array rather than the error. This is the same text/JSON split described in
  `Modules-Keyboard`.
- **The pool state is the library's own string.** `ONLINE`, `DEGRADED`, `FAULTED`, `OFFLINE`,
  `UNAVAIL` and `REMOVED` are passed through verbatim, and a property that cannot be read becomes
  `unknown` — so `{state}` is not a fixed enumeration that a format can match on exhaustively.
- **A key that is a single space hides the key and the separator.** The default key contains the pool
  name, which means it is also the only place the pool name appears when no `format` is set.

## Implementation

`ffDetectZpool()` loads `libzfs` with `FF_LIBRARY_LOAD_MESSAGE(libzfs, "libzfs", 6)` and resolves
`libzfs_init`, `zpool_name_to_prop`, `zpool_iter`, `libzfs_fini`, `zpool_get_prop_int`,
`zpool_get_prop` and `zpool_close` individually, so a library that is present but incomplete fails
with the name of the missing symbol. The eight property identifiers it needs — `name`, `health`,
`guid`, `size`, `free`, `allocated`, `fragmentation`, `readonly` — are looked up by name through
`zpool_name_to_prop` and stored as ids, and a negative result aborts with
`Failed to query prop: <name>`. `zpool_iter()` then walks the pools, and the callback fills one
`FFZpoolResult` per pool and closes the handle it was given. The `libzfs` handle is released through a
`gnu::cleanup` function, so it is freed on the error paths as well.

`src/detection/zpool/zpool.h` includes `modules/zpool/option.h`, so the detection layer sees the
module's options struct — although this detector takes no options: `ffDetectZpool()` receives only the
result list, and `ffGenerateZpoolJsonResult()` marks its options parameter `[[maybe_unused]]`.

`ffPrintZpool()` builds the key itself (the pool name in parentheses, or just `Zpool` when the name is
empty), computes the two percentages from the byte counts, and renders either the default line or the
format string. `ffPercentAppendNum()` is called directly for the default line, which is why the
numbers appear there regardless of `percent.type`, while the format path fills each percentage
variable behind its own `percent.type` test.
