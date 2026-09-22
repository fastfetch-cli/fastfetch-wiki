# Logo

> Query built-in logo for JSON output

| | |
|---|---|
| Module type | `logo` |
| Default order | — (no `defaultOrder`) |
| Module source | `src/modules/logo/logo.c` |
| Detection source | — (reads the logo tables in `src/logo/`) |

Exports the *built-in ASCII logo* that fastfetch would draw, as JSON, so that another program can
render it. It has no console output at all:

```
Logo: Supported in JSON format only
```

The module exists to answer “what is the logo of the detected system, and what are its color
placeholders?” from a script.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Everywhere | `src/logo/ascii/*.inc`, `src/logo/logo.c` | Platform independent |

No detection layer. Which logo is exported depends only on the `logo.source` / `logo.type` settings
(see below), not on the compiling platform.

## Configuration

| Option | Type | Default | Description |
|---|---|---|---|
| `condition` | object | – | See [Configuration](Modules/Global#condition-in-practice). |

That is the *only* option the module's JSON parser accepts. `type` is silently ignored (the module
has no per-module type setting of its own). The logo to export is chosen by the **global** `logo`
settings, not by module options:

| Setting | Values | Meaning |
|---|---|---|
| `logo.type` | `auto`, `builtin`, `small`, … | Which kind of logo to export. Only `auto`, `builtin` and `small` are exportable; anything else is an error. |
| `logo.source` | a logo name | Export this specific logo by name (e.g. `ubuntu`, `debian`, `macOS_small`). Empty means “the detected one”. |

Both are global, so they apply to the whole run:

```jsonc
{ "logo": { "source": "debian" }, "modules": [ "logo" ] }
```

## Format string

None. The module has no key and no format string.

## JSON output

```jsonc
{
  "type": "Logo",
  "result": {
    "lines": "                     ..'\n                 ,xNMM.\n               .OMMMMo\n ...",
    "names": [ "macOS" ],
    "colors": [ "32", "33", "91", "31", "35", "34" ],
    "colorKeys": "33",
    "colorTitle": "32",
    "type": [ "normal" ]
  }
}
```

| Field | Meaning |
|---|---|
| `lines` | The logo art. `\n` separates rows. `$1` … `$9` are placeholders for `colors[0]` … `colors[8]`. |
| `names` | All names this logo answers to. The first is the canonical one. |
| `colors` | ANSI SGR parameters (without the `\e[` … `m` wrapper) for the `$N` placeholders. |
| `colorKeys` | SGR parameter for the module keys next to the logo. |
| `colorTitle` | SGR parameter for the title line. |
| `type` | Which variants of the logo this entry describes. See [Pitfalls](#pitfalls). |

Failures are reported as `error` and no `result`:

```jsonc
{ "type": "Logo", "error": "No built-in logo found for the specified name/size" }
{ "type": "Logo", "error": "Only 'builtin' and 'small' logo types are supported" }
```

## Examples

The detected system's logo:

```jsonc
{ "modules": [ "logo" ] }
```

A specific distribution's logo, whatever OS you are on:

```jsonc
{ "logo": { "source": "ubuntu" }, "modules": [ "logo" ] }
```

The small variant:

```jsonc
{ "logo": { "type": "small" }, "modules": [ "logo" ] }
```

## Pitfalls

- **The module never prints to the console.** Running `fastfetch -s logo` only shows
  `Supported in JSON format only`. It is meaningful only with `--format json`.
- **`--logo <value>` takes a logo *name*, not a type.** Only `none` and `small` are recognised
  specially; every other value is stored as the logo source. So `--logo auto` asks for a built-in
  logo literally named `auto` and fails with
  `No built-in logo found for the specified name/size` — even though `auto` is a valid *type*. Use
  `--logo-type auto` / `--logo-type builtin` to change the type. The same trap applies to
  `--logo chafa`, `--logo sixel`, `--logo iterm`, `--logo raw` and friends.
- **`type` describes the variant, and a regular logo reports `["normal"]`.** `NORMAL` is `0`, so it
  cannot be tested with `&`; the module compares against it instead. The other two entries are bit
  tests, so they combine:

  ```jsonc
  { "logo": { "source": "Alpine2" } }        // -> "type": [ "alter" ]
  { "logo": { "source": "alpine2_small" } }  // -> "type": [ "small", "alter" ]
  ```
- **`--logo none` is an error, not an empty result.** The module rejects the `none` type with
  `Only 'builtin' and 'small' logo types are supported` instead of returning an empty logo.
- **`lines` is raw art, not terminal output.** Color placeholders are *not* substituted — the JSON
  gives you `$1` and leaves the mapping to you. The art also assumes a Nerd Font only for the icon of
  a *builtin image*; the ASCII art itself is plain.
- **`colorKeys` / `colorTitle` are SGR fragments.** Insert them as `\e[<value>m`; they are not full
  escape sequences and there is no reset in them.
- **`type: "small"` does not derive a small variant — it selects one by name.** The small art of a
  distribution is registered as its own entry (`alpine2_small`, not a scaled `Alpine2`), so
  `{ "logo": { "type": "small", "source": "Alpine2" } }` fails with
  `No built-in logo found for the specified name/size`. Omit `type` and let the name pick the art.

## Implementation

`ffPrintLogo()` does nothing but report the error — the console path is
deliberately unimplemented.

All the work is in `ffGenerateLogoJsonResult()`:

1. Map the global `logo.type` onto an `FFLogoSize`:
   `FF_LOGO_TYPE_SMALL` → `FF_LOGO_SIZE_SMALL`; anything that is not `FF_LOGO_TYPE_BUILTIN` and not
   `FF_LOGO_TYPE_AUTO` → error out.
2. Look the logo up:
   - `logo.source` non-empty → `ffLogoGetBuiltinForName(&source, size)`
   - otherwise → `ffLogoGetBuiltinDetected(size)`

   A miss returns `No built-in logo found for the specified name/size`.
3. Emit the fields. `names`, `colors` and `type` are arrays; the loops stop at the
   `FASTFETCH_LOGO_MAX_NAMES` / `FASTFETCH_LOGO_MAX_COLORS` sentinels (`nullptr`).

The `type` array is built by two bit tests and one comparison:

```c
if (logo->type == FF_LOGO_LINE_TYPE_NORMAL)   yyjson_mut_arr_add_str(doc, typeArr, "normal");
if (logo->type & FF_LOGO_LINE_TYPE_SMALL_BIT) yyjson_mut_arr_add_str(doc, typeArr, "small");
if (logo->type & FF_LOGO_LINE_TYPE_ALTER_BIT) yyjson_mut_arr_add_str(doc, typeArr, "alter");
```

`NORMAL` is `0` (`src/logo/logo.h`), so a bit test for it can never be true — the equality comparison
is what makes a regular logo report `"normal"` instead of an empty array. The other two are genuine
bits, which is why they can appear together.

Note that the module's `parseJsonObject` swallows `type` and `condition` and routes everything else
through `ffPrintError()`, so an unknown key here is silent unless `display.showErrors` is enabled.
