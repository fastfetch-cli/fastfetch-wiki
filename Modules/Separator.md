# Separator

> Print a separator line

| | |
|---|---|
| Module type | `separator` |
| Default order | 2 — only used by `--gen-config` |
| Module source | `src/modules/separator/separator.c` |
| Detection source | — |

Prints a horizontal rule. By default its length matches the *title* above it, so it underlines
`user@my-pc` exactly:

```
user@my-pc
---------------------
```

The rule is built from the `string` option (default `-`) repeated to fill the title's width. If
`string` is longer than one character, whole copies are written while they still fit and the last
copy is cut mid-string at a UTF-8 character boundary.

This module has **no key, no icon and no format string** — see [Pitfalls](#pitfalls).

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Everywhere | `src/modules/separator/separator.c`, `src/logo/logo.c` | Platform independent |

There is no detection layer at all. The only OS-dependent input is the platform struct used to size
the rule.

## Configuration

| Option | Type | Default | Description |
|---|---|---|---|
| `string` | string | `"-"` | The text to repeat. Any length; multi-byte UTF-8 is handled. |
| `times` | integer | `0` | How many times to repeat `string`. `0` means “auto”, i.e. match the title width. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `condition` | object | – | See [Configuration](Modules/Global#condition-in-practice). |

That is the complete list. `key`, `keyColor`, `keyIcon`, `keyWidth` and `format` are **not** accepted
by this module; writing them is an unknown key (see [Pitfalls](#pitfalls)).

`times` counts **repetitions of the whole `string`**, not characters:

```jsonc
{ "modules": [ { "type": "separator", "string": "=-=", "times": 3 } ] }
```

```
=-==-==-=
```

## Format string

None. `fastfetch -h separator-format` has nothing to show, and `format` is an invalid key here.

## JSON output

The module has no JSON representation:

```jsonc
{ "type": "Separator", "error": "Unsupported for JSON format" }
```

`--structure separator --format json` therefore produces an error object rather than a result.

## Examples

```jsonc
{
  "modules": [
    { "type": "title" },
    { "type": "separator" },
    { "type": "os" }
  ]
}
```

A fixed-length rule instead of the automatic one:

```jsonc
{ "type": "separator", "string": "─", "times": 40 }
```

A colored rule:

```jsonc
{ "type": "separator", "outputColor": "yellow" }
```

## Pitfalls

- **`times` is range-checked, and `-1` is rejected.** An out-of-range value prints
  `Property 'times' must be a non-negative integer no greater than 4294967295` and the module falls
  back to the automatic width, so a bad value no longer floods the terminal. Use `0` for “auto” — a
  negative number is not a shorthand for it.
- **The automatic width follows the *unformatted* title.** It is computed from the user name and
  host name, not from the title's actual output. If you give [Title](Modules/Title) a `format`, the
  separator no longer matches:

  ```
  a very long formatted title indeed
  ---------------------
  ```

- **The automatic width depends on the host name being measured in bytes.** The host name contributes
  its byte length (or the byte index of the first `.`), while the user name contributes its *display
  width*. A host name with non-ASCII characters therefore yields a rule that is too long.
- **Printing a separator without a title uses a non-FQDN host name.** The FQDN flag is stored by the
  Title module as a side effect; if Title does not run, the separator assumes the short form.
- **`outputColor` applies to the rule only.** There is no key to color, so `keyColor` is meaningless
  here.
- **Unknown keys are silent.** `{"type": "separator", "key": "-"}` is accepted, ignored and produces
  no message unless `display.showErrors` is enabled — see
  [Global](Modules/Global#pitfalls-that-bite-across-modules).
- **`length` is a renamed option and only prints a deprecation notice.** It is still accepted so that
  old configs are not silently meaningless:

  ```
  Separator: The option length has been renamed to times.
  ```

  The rule is then rendered with the *default* `times`, i.e. auto width.

## Implementation

`ffPrintSeparator()` (`src/modules/separator/separator.c`) runs in four steps:

1. `ffLogoPrintLine()` — advances the logo so the rule lines up beside an image logo rather than
   overwriting it.
2. `ffPrintColor(&options->outputColor)` when the color is set and not in `--pipe` mode.
3. The body. There are two paths:
   - **`times > 0`**: `ffPrintCharTimes()` when `string` is a single byte, otherwise the string is
     `fputs`-ed `times` times. Note the single-byte fast path uses `string.chars[0]`, so a
     multi-byte `string` with `times > 0` repeats whole strings and cannot cut mid-character.
   - **`times == 0` (auto)**: `titleLength` is
     `1 + ffUtf8StrWidth(userName) + (titleFqdn ? hostName.length : index-of-first-dot(hostName))`.
     Whole copies of `string` are written while `remaining >= wcsLength`; the remainder is then
     filled by walking `ffUtf8CharLenWidth()` character by character and stopping once the remaining
     display width would be exceeded. For a single-byte `string` this reduces to
     `ffPrintCharTimes(string[0], titleLength)`.
4. A reset (when colored) and `putchar('\n')`.

`options->times` goes through the shared `ffJsonConfigParseUInt32()` helper, which reads the value
with `yyjson_get_sint()` and rejects anything outside `[0, 4294967295]`. The helper exists because
`yyjson_get_uint()`, the obvious choice, also accepts the signed subtype and reinterprets its bits: a
bare `(uint32_t) yyjson_get_uint(val)` turns `-1` into `UINT32_MAX`, and `ffPrintCharTimes()` — which
lives in `src/common/impl/printing.c`, writes in blocks and takes the count as-is — would then emit
4 294 967 295 characters. The bound is enforced at parse time only, so `ffPrintCharTimes()` itself
will still write whatever it is handed. `cpuusage`, `netio` and `diskio` use the same helper for
their `waitTime`, where the same mistake used to mean a sleep of roughly 50 days.

The schema documents `times` as `minimum: 0` with no upper bound, so the runtime is slightly stricter
than the schema here: a value above `4294967295` validates but is rejected at runtime.
