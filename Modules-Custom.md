# Custom

> Print a custom string, with or without key

| | |
|---|---|
| Module type | `custom` |
| Default order | — (no `defaultOrder`) |
| Module source | `src/modules/custom/custom.c` |
| Detection source | — |

Prints a literal string. It is the module you want for a static line, a separator-like label, or a
value that comes from the environment:

```
hello
```

The default key is a single space, so nothing precedes the text. Give it a key and you get an
ordinary `key: value` line:

```jsonc
{ "modules": [ { "type": "custom", "key": "Note", "format": "hello" } ] }
```

```
Note: hello
```

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Everywhere | `src/modules/custom/custom.c` | Platform independent |

No detection layer, no OS calls — the text is taken verbatim from the config.

## Configuration

| Option | Type | Default | Description |
|---|---|---|---|
| `format` | string | – | The text to print. **This is where your text goes**, not `text`. |
| `key` | string | `" "` | Key text. A single space hides the key *and* its separator. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyIcon` | string | built-in glyph | The icon printed when `display.key.type` includes the icon bit. Set it to any glyph you like, or to `""` to print none. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `condition` | object | – | See [Configuration](Modules-Global#condition-in-practice). |

The keys above are the entire accepted set. `text` is **not** a valid key here — that is the
[Command](Modules-Command) module's option.

Beware of the `key` default: because the initial key is a single space, a `custom` module without an
explicit key prints only its text. Writing `"key": ""` does *not* hide it either — see
[Pitfalls](#pitfalls).

## Format string

`custom` publishes **no format variables**; `fastfetch -h custom-format` prints nothing.

The `format` string is still parsed as a format string, so it has access to the global syntax that
does not depend on module arguments:

| Syntax | Meaning |
|---|---|
| `{$NAME}` | Value of the environment variable `NAME` (empty if unset) |
| `{$1}` … `{$N}` | Constant from `display.constants` (1-based; negative counts from the end) |
| `{#RRGGBB}`, `{#name}` | Inline color escape |
| `{{` | A literal `{` |

Note the `$` in `{$NAME}`: `{HOME}` is **not** substituted and is printed literally as `{HOME}`.

```jsonc
{ "modules": [ { "type": "custom", "format": "HOME={$HOME} NOPE={$DOES_NOT_EXIST}" } ] }
```

```
HOME=/home/user NOPE={$DOES_NOT_EXIST}
```

An unset variable leaves the placeholder visible, which is a useful sanity check.

## JSON output

The JSON result is the rendered format string — the same value the console prints:

```jsonc
{ "type": "Custom", "result": "hello" }
```

Placeholders are resolved first, so `format: "HOME={$HOME}"` exports the expanded text, and a
`qjs:` / `lua:` format exports the **script's output**, not the script itself.

A failure inside such a script is reported as `error` with no `result`:

```jsonc
{ "type": "Custom", "error": "Qjs runtime error: SyntaxError: expecting ';'" }
```

A *plain* format never fails, though: an unknown placeholder such as `{bogus}` is emitted verbatim
rather than treated as an error, so it lands in `result` unchanged.

## Examples

A static label with a value that comes from the environment:

```jsonc
{
  "modules": [
    { "type": "custom", "key": "Projects", "format": "{$HOME}/projects" }
  ]
}
```

A heading with inline color — note the single closing brace, since only `{{` is an escape and a lone
`}` is literal:

```jsonc
{ "type": "custom", "format": "{#blue}{{ System }{#}" }
```

A blank line as spacing:

```jsonc
{ "type": "custom", "key": " ", "format": " " }
```

Constants are handy for repeated values:

```jsonc
{
  "display": { "constants": ["/home/user/projects"] },
  "modules": [ { "type": "custom", "key": "Projects", "format": "{$1}" } ]
}
```

## Pitfalls

- **`"key": ""` shows the module name, it does not hide it.** The runtime falls back to
  `Custom: hello` when the key is empty, so a single space is what hides the key *and* its separator.
  The shared `key` definition in `doc/json_schema.json` says exactly that — “use a single space to
  hide the key” — and `custom` now reuses it rather than carrying its own wording.
- **The JSON `result` is the rendered text, not your `format` string.** Placeholders are already
  expanded and a `qjs:`/`lua:` script has already run, so the raw template is not recoverable from
  `--format json`; use `--gen-config` if you need the config form back. `break`, `colors` and
  `separator` remain the only modules with no JSON result at all.
- **`format` is parsed, not printed verbatim.** A `{` that is not part of a placeholder must be
  written `{{`, and any `{$…}` sequence is resolved. A Windows path such as `C:\{$X}` is a footgun;
  prefer forward slashes or escape the brace.
- **An `#`-style color inside `format`** is emitted as `\e[<color>m` and relies on the following
  text to reset. Follow it with `{#}` or a named color to avoid bleeding into the next line.
- **`\e` only works in a `.json5` config.** `.json` and `.jsonc` are read with the strict reader,
  which rejects the escape with `invalid escaped sequence in string`. `{#name}` / `{#RRGGBB}` (as used
  above) works in every dialect, as does `\u001b`. See
  [Global](Modules-Global#pitfalls-that-bite-across-modules).
- **`custom` has no detection layer**, so it never fails and never reports an error — a mistake in
  `format` is invisible. Test with `--pipe` to see the raw output.
- **Module options are JSON-only.** There is no `--custom-format` flag any more.

## Implementation

`ffPrintCustom()` is a two-line function:

```c
bool ffPrintCustom(FFCustomOptions* options) {
    ffPrintFormat(FF_MODULE_GET_DISPLAY_NAME(Custom), 0, &options->moduleArgs, FF_PRINT_TYPE_DEFAULT, 0, ((FFformatarg[]) {}));
    return true;
}
```

It delegates everything to `ffPrintFormat()` (`src/common/impl/format.c`) with **zero** format
arguments — that is why no `{variable}` resolves and why `fastfetch -h custom-format` has nothing to
list. What does still work is the argument-independent syntax: the `{$…}` branch of the parser reads
`getenv()` for a name and `instance.config.display.constants` for an index, neither of which depends
on the module's argument list.

`ffInitCustomOptions()` sets the icon and then `ffStrbufSetStatic(&options->moduleArgs.key, " ")`, so
the key is a space rather than empty. `FFCustomOptions` holds nothing but `moduleArgs`
(`src/modules/custom/option.h`), so the whole configuration surface is the seven shared keys.

`ffParseCustomJsonObject()` accepts exactly what `ffJsonConfigParseModuleArgs()` understands and
reports anything else through `ffPrintError()`, which is silent unless `display.showErrors` is set.
