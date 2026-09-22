# Colors

> Display the terminal's 16-color palette

| | |
|---|---|
| Module type | `colors` |
| Default order | 73 — only used by `--gen-config` |
| Module source | `src/modules/colors/colors.c` |
| Detection source | — |

Prints a swatch of the terminal's 16 ANSI colours, so a screenshot shows which palette the terminal
is actually using. Nothing is detected — the module only emits escape sequences and lets the
terminal render them.

The default settings (`symbol: "background"`, `block.width: 3`, `block.range: [0, 15]`) produce two
rows of eight cells, each cell a three-space run painted with a background colour. Escape (0x1B) is
written `\e` below; `--logo none` was used so no key block is in the way:

```
\e[40m   \e[41m   \e[42m   \e[43m   \e[44m   \e[45m   \e[46m   \e[47m   \e[m
\e[100m   \e[101m   \e[102m   \e[103m   \e[104m   \e[105m   \e[106m   \e[107m   \e[m
```

The first row is colours 0–7 (`\e[40m`–`\e[47m`), the second is 8–15 (`\e[100m`–`\e[107m`). With
`symbol: "block"` the same 16 colours come out as foreground `█` runs instead:

```
\e[30m███\e[31m███\e[32m███\e[33m███\e[34m███\e[35m███\e[36m███\e[37m███\e[m
\e[90m███\e[91m███\e[92m███\e[93m███\e[94m███\e[95m███\e[96m███\e[97m███\e[m
```

Unlike [Break](Modules/Break) and [Separator](Modules/Separator), this module *does* have a key
block — but the default key is a **single space**, which is the magic value that hides both the key
and the `: ` separator. That is why the default output starts straight with the swatch.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Everywhere | `src/modules/colors/colors.c` | Platform independent; one Linux-only detail, see below |

There is no detection layer. The palette is whatever the terminal implements, and the module never
queries it. The single platform-dependent piece is a Linux-console workaround in the background
branch: when `$TERM` is set and does not start with `xterm`, the module emits `\e[5m` before the
bright half, because the Linux console needs blink enabled for a light background to render at all
(`#ifdef __linux__` in `ffPrintColors()`).

## Configuration

| Option | Type | Default | Description |
|---|---|---|---|
| `symbol` | enum | `"background"` | `block`, `background`, `circle`, `diamond`, `triangle`, `square` or `star` |
| `block` | object | – | Only honoured by `block` and `background` |
| `block.width` | integer | `3` | Symbol repetitions (or spaces) per colour, `1`–`9` |
| `block.range` | `[start, end]` | `[0, 15]` | Which colour indices to print; both ends inclusive, `0`–`15` |
| `brightness` | enum | `"default"` | `default`, `normal` or `light` |
| `paddingLeft` | integer | `0` | Spaces printed before the swatch, on every row; `0`–`4294967295` |
| `key` | string | `" "` | A single space hides the key and the separator |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | – | Used when `display.key.type` includes the icon bit |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `condition` | object | – | See [Global](Modules/Global#condition-in-practice) |

`symbol` decides which of two code paths runs, and the two paths do not share options:

- **`block` and `background`** use `block.width` and `block.range`. `block` paints `█` with a
  foreground colour, `background` paints spaces with a background colour.
- **Everything else** prints a fixed one-character symbol followed by a space, always eight of them,
  and ignores `block` entirely — `block.range` cannot narrow them and `block.width` cannot widen
  them.

`brightness` decides which half of the 16 colours is used:

| `brightness` | `block` / `background` | other symbols |
|---|---|---|
| `default` | colours `0`–`7` on the first row, `8`–`15` on the second | the 256-colour palette, indices **8 down to 1**, on one row |
| `normal` | colours `0`–`7` only, one row | `\e[30m`–`\e[37m`, one row |
| `light` | colours `8`–`15` only, one row | `\e[90m`–`\e[97m`, one row |

`paddingLeft` is re-emitted on each row rather than once, so it stays aligned under the first cell.

`format` and `outputColor` are accepted by the shared module-argument parser but have **no effect**
here, and neither is in the schema for this module — see [Pitfalls](#pitfalls).

## Format string

None. The module never calls the format engine, and asking for the list fails:

```
$ fastfetch -h colors-format
Error: Module 'Colors' doesn't support output formatting
```

The message goes to stderr and the exit status is still 0.

## JSON output

There is no JSON representation:

```jsonc
{ "type": "Colors", "error": "Unsupported for JSON format" }
```

This is expected rather than a gap: the module's entire output is terminal state, which has no
meaning in a data document.

## Examples

The default palette with a visible key:

```jsonc
{ "type": "colors", "key": "Palette" }
```

A narrower dark-only swatch with wider cells:

```jsonc
{
  "type": "colors",
  "symbol": "block",
  "block": { "width": 6, "range": [0, 3] },
  "brightness": "normal"
}
```

The classic round-dot palette over the full 256-colour ramp:

```jsonc
{ "type": "colors", "symbol": "circle", "paddingLeft": 2 }
```

Indented to line up with an image logo:

```jsonc
{ "type": "colors", "symbol": "square", "paddingLeft": 4, "brightness": "light" }
```

## Pitfalls

- **`block` is ignored by every symbol except `block` and `background`.** `{ "type": "colors",
  "symbol": "star", "block": { "range": [0, 3] } }` still prints eight stars, because the
  non-block branch has no range and no width.
- **The default key is a single space, so the default output has no key.** That is deliberate —
  `" "` is the documented magic value for hiding a key. Set `key: ""` to get the module name back,
  or any other string to label the swatch.
- **`symbol: "block"` loses all colour when stdout is not a terminal.** `display.pipe` defaults to
  `!isatty(STDOUT_FILENO) || getenv("NO_COLOR")` (`src/options/display.c`), and for `block` the
  module skips the `\e[3x`/`\e[9x` codes whenever that flag is set. A piped run, a redirect, a
  captured screenshot — or simply having `NO_COLOR` in the environment — therefore shows plain `█`
  characters with no colour at all:

  ```
  ████████████████████████
  ████████████████████████
  ```

  `background` is not affected — its background codes are emitted unconditionally, so the piped
  output is identical to the terminal output. Use `background` if you need to pipe the swatch.
- **`paddingLeft` and `block.width` are range-checked, and an out-of-range value is reported rather
  than used.** Both go through `ffJsonConfigParseUInt32()`, so a negative number, a fraction or a
  value above the limit is rejected and the module falls back to the built-in width of `3`:

  ```
  Colors: Property 'paddingLeft' must be a non-negative integer no greater than 4294967295
  Colors: Property 'block.width' must be an integer between 1 and 9
  ```

  The schema declares the same bounds (`minimum: 1` / `maximum: 9` on `block.width`, and a
  `maximum` on `paddingLeft`), and each message is printed once per run. Type a value outside the
  range and the swatch still prints — with the default width, not the one you asked for.
- **An out-of-range or non-integer `block.range` prints an error and silently falls back to the
  default.** A reversed range gets its own message:

  ```
  Colors: Property 'block.range' must be an array of two integers between 0 and 15
  Colors: Invalid block.range value: range[0] > range[1]
  ```

  The first message covers both a non-integer element — `[1.5, 3]`, `["x", 3]`, `[true, 3]`,
  `[null, 3]` — and an element outside `0`–`15`, such as `[3, 16]`, `[-1, 3]` or `[3, -1]`. The
  second is what a reversed but otherwise valid range like `[5, 3]` produces. The module then prints
  the full `[0, 15]` palette, so a typo looks like "the option was ignored" rather than "the value was
  rejected".
- **An invalid `symbol` also falls back, with a less informative message:**
  `Colors: Invalid symbol value: Invalid enum string`. The fallback is the default `background`.
- **`format` and `outputColor` are accepted but do nothing.** `{ "type": "colors", "format": "XXX" }`
  produces byte-identical output to `{ "type": "colors" }`, and the same is true of `outputColor`:
  the swatch's own colour codes overwrite it immediately. Neither key is in the schema's entry for
  this module, so a schema-validating editor will flag them while the runtime stays quiet.
- **`Nothing to print` is the catch-all failure, and a brightness/range mismatch is how you hit it.**
  The module builds the whole swatch in a buffer first and errors at the end if the buffer is empty.
  With `block` or `background` each half is skipped when the brightness does not cover it, so
  `brightness: "light"` with `range: [0, 7]`, or `brightness: "normal"` with `range: [8, 15]`, has
  nothing left to emit:

  ```
  Nothing to print
  ```

  The message only appears when `display.showErrors` is true, so on a default run the module just
  vanishes and the line stays blank.

## Implementation

`ffPrintColors()` (`src/modules/colors/colors.c`) branches once on `symbol`, builds the entire
swatch into a local `FFstrbuf`, and prints it only at the end — which is what makes the
`Nothing to print` check possible.

### `block` / `background`

Two loops over `block.range`, split at brightness 8:

- the dark half is `block.range[0] … min(block.range[1], 7)`, written as `\e[3{d}m` for `block` or
  `\e[4{d}m` for `background` (skipped entirely when `brightness == light`);
- the bright half is `max(block.range[0], 8) … block.range[1]`, written as `\e[9{d-8}m` or
  `\e[10{d-8}m` (skipped entirely when `brightness == normal`).

Each colour writes `block.width` copies of either `█` or a space. For `block`, the colour code is
suppressed in pipe mode; for `background` it is not. Both rows end with a reset once a colour has
been emitted.

The Linux console workaround sits between the two halves: for `background` only, a `$TERM` that is
set and does not start with `xterm` gets `\e[5m`. (A terminal multiplexer can override `$TERM`, which
is why the check is a prefix test rather than an equality test.)

### other symbols

One loop, with a hard-coded symbol table: `"● "`, `"◆ "`, `"▲ "`, `"■ "`, `"★ "`, and `"███ "` as the
fallback. `brightness: default` walks 256-colour indices **downwards** from 8 to 1 (giving the
bright-to-dark row the default look implies); `normal` and `light` walk `0…7` with `\e[3{d}m` and
`\e[9{d}m` respectively. The trailing space of the last symbol is trimmed, so the row does not end in
a gap.

### key and padding

`ffPrintLogoAndKey()` is called immediately before the swatch, with `FF_PRINT_TYPE_DEFAULT`, so
`key`, `keyColor`, `keyIcon` and `keyWidth` all behave as on any other module — except that the
default key of `" "` short-circuits the whole block (see `ffPrintLogoAndKey()` in
`src/common/impl/printing.c`, which treats `" "` as a magic value for a hidden key).
`paddingLeft` is written with `ffPrintCharTimes(' ', …)` once per row.

### option parsing

`ffParseColorsJsonObject()` is the only place the options are validated, and every rejection leaves
the field at its default, so no invalid value ever reaches `ffPrintColors()`:

- `symbol` and `brightness` go through `ffJsonConfigParseEnum()`, which accepts the documented
  strings as well as the matching enum integers.
- `paddingLeft` goes through `ffJsonConfigParseUInt32(val, &out, UINT32_MAX)` and `block.width`
  through the same helper with a limit of `9`; the helper requires an integer and rejects anything
  outside `[0, max]`, which is what keeps a negative number from wrapping around to a huge unsigned
  one.
- `block.range` is checked by hand: the value has to be an array of exactly two elements, the first
  must not exceed the second, and the second must not exceed `15`.

`ffJsonConfigParseModuleArgs()` runs first for every key, which is why `key`, `keyColor`, `keyIcon`,
`keyWidth`, `outputColor`, `format` and `condition` are accepted here even though `format` and
`outputColor` do nothing.
