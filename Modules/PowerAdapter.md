# PowerAdapter

> Print power adapter name and charging watts

| | |
|---|---|
| Module type | `poweradapter` |
| Default order | 46 (only used by `--gen-config`) |
| Module source | `src/modules/poweradapter/poweradapter.c` |
| Detection source | `src/detection/poweradapter/` |

Prints one line per detected mains power supply. The default line is the adapter's **name**, and
falls back to its wattage when the name is empty:

```
Power Adapter: 65W
```

That fallback is the normal case on Linux — see the pitfalls. Everything else the detection layer
reads (manufacturer, model, description, serial) is only reachable through a custom `format` or the
JSON output.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `poweradapter_linux.c` | Reads `/sys/class/power_supply/` |
| macOS | `poweradapter_apple.c` | `IOPSCopyExternalPowerAdapterDetails()` |
| Windows | `poweradapter_nosupport.c` | Reports `Not supported on this platform` |
| Android | `poweradapter_nosupport.c` | Not supported, even though it is a Linux kernel |
| FreeBSD / MidnightBSD / DragonFly | `poweradapter_nosupport.c` | Not supported |
| NetBSD | `poweradapter_nosupport.c` | Not supported |
| OpenBSD | `poweradapter_nosupport.c` | Not supported |
| Solaris / illumos | `poweradapter_nosupport.c` | Not supported |
| Haiku | `poweradapter_nosupport.c` | Not supported |
| GNU/Hurd | `poweradapter_nosupport.c` | Not supported |

Only Linux and macOS have an implementation — Android compiles the nosupport stub despite having a
Linux kernel. Elsewhere the module reports
`Not supported on this platform`, which becomes the JSON `error` and — because
`display.showErrors` defaults to `false` — makes the text run print nothing at all.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | `Power Adapter` | Module key. A single space hides the key and the separator |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

There is **no module-specific option**. Every key other than the generic ones produces
`Unknown JSON key …`.

## Format string

Run `fastfetch -h poweradapter-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{watts}` | Power adapter watts |
| `{name}` | Power adapter name |
| `{manufacturer}` | Power adapter manufacturer |
| `{model-name}` | Power adapter model |
| `{description}` | Power adapter description |
| `{serial}` | Power adapter serial number |

None of the six is marked `*` in the help output, so none is available in the key format.

`{watts}` is a plain integer with no unit — `65`, not `65W`. The default line is the only place the
`W` suffix appears, and it is appended by the module itself.

## JSON output

```jsonc
[
    {
        "type": "PowerAdapter",
        "result": [
            {
                "description": "",
                "manufacturer": "Apple Inc.",
                "modelName": "",
                "name": "",
                "serial": "0",
                "watts": 65
            }
        ]
    }
]
```

- `result` is always an array, one object per adapter. When detection fails the module carries
  `{"type": "PowerAdapter", "error": "…"}` instead, with no `result` key.
- `watts` is a number; the five other fields are strings and are `""` when unknown — never `null`
  and never omitted.
- The key inside the object is **`modelName`**, not `model-name`: the JSON uses camelCase while the
  format variable uses a hyphen.
- The key order inside each object is `description`, `manufacturer`, `modelName`, `name`, `serial`,
  `watts`.

## Examples

```jsonc
// Watts with the unit, plus the manufacturer
{ "type": "poweradapter", "format": "{watts}W ({manufacturer})" }
```

```jsonc
// The model and serial, skipping everything else
{ "type": "poweradapter", "format": "{model-name} #{serial}" }
```

```jsonc
// Rename the line
{ "type": "poweradapter", "key": "Charger" }
```

## Pitfalls

- **On Linux the name is always empty, so the default line is always a wattage.** The Linux backend
  fills `watts`, `manufacturer`, `modelName` and `serial` but never touches `name`, and
  `description` stays empty as well. `Power Adapter: 65W` is therefore the only shape the default
  line can take there, and a config that wants a nicer line has to set `format` explicitly.
- **An empty result is an error in text but an empty array in JSON.** The text path reports
  `No power adapters found` when the list is empty; the JSON path has no such check and emits
  `{"result": []}` with no `error`. The same divergence exists in `keyboard` and `mouse`.
- **Linux only reports adapters that are online right now.** A supply is skipped when `present` is
  `0`, when `type` is not exactly `Mains`, when `scope` is `Device`, or when `online` is not `1`. A
  charger that is plugged in but not currently supplying power therefore disappears, and so does a
  battery (whose `type` is `Battery`).
- **`watts` on Linux comes from `input_power_limit`, not from a nameplate rating.** The sysfs value
  is in microwatts and is divided by 1e6 and rounded, so it is the kernel's current input power
  limit. On a machine that negotiates a lower limit the number changes, and a supply that does not
  expose `input_power_limit` at all is skipped entirely.
- **`watts` can legitimately be `0`.** Both backends initialise it to `0` and only overwrite it when
  the corresponding key is present, so `0` means "unknown" rather than "no power".
- **macOS reports nothing at all when running on battery.** `IOPSCopyExternalPowerAdapterDetails()`
  returns `NULL` with no adapter connected, which is the common case — the module then falls into
  the empty-list branch rather than reporting an error.
- **On macOS the model and serial have numeric fallbacks.** When the `Model` string is missing the
  adapter id (`kIOPSPowerAdapterIDKey`) is formatted as a decimal number instead, and when
  `SerialString` is missing the serial number is formatted as **upper-case hex** (`"%X"`, no `0x`
  prefix). Both fallbacks leave a numeric-looking string where a name would be expected.
- **Windows, the BSDs, Solaris, Haiku and GNU/Hurd have no implementation**, so on those platforms
  the module never prints anything unless `display.showErrors` is `true`.

## Implementation

`ffDetectPowerAdapter()` appends `FFPowerAdapterResult` values (five `FFstrbuf`s — `description`,
`name`, `modelName`, `manufacturer`, `serial` — plus an `int watts`) to a list and returns an error
string. `ffPrintPowerAdapter()` and `ffGeneratePowerAdapterJsonResult()` each call it and each
release the strings of every element afterwards; nothing is cached, so a `--dynamic-interval` run
re-reads the supply state on every round.

With more than one adapter the lines are numbered by index, and with a single adapter the bare key is
printed.

### Linux

`/sys/class/power_supply/` is enumerated with `opendir()` and each entry is opened with `openat()`.
An entry is kept only when all of these hold: `present` is not `0`, `type` is exactly `Mains`
(after trimming trailing whitespace), `scope` is either missing or not `Device`, `online` is `1`, and
`input_power_limit` exists and is non-empty. `watts` is `input_power_limit / 1e6` rounded to the
nearest integer. `manufacturer`, `model_name` and `serial_number` are read and trimmed; when
`manufacturer` is missing and the directory is named `macsmc-ac` (Apple Silicon under Asahi) it is
set to `Apple Inc.` `name` and `description` are left empty.

### macOS

`IOPSCopyExternalPowerAdapterDetails()` is called once and, when the dictionary contains
`kIOPSPowerAdapterWattsKey`, a single adapter is produced. `name` comes from `kIOPSNameKey`,
`manufacturer` from `Manufacturer`, `description` from `Description`, and `watts` from
`kIOPSPowerAdapterWattsKey`. `modelName` comes from the `Model` string with the numeric adapter id as
a fallback, and `serial` from `SerialString` with the numeric serial in hex as a fallback. Note that
this backend can only ever produce **one** adapter, whereas the Linux one produces as many as sysfs
exposes.
