# Break

> Print an empty line

| | |
|---|---|
| Module type | `break` |
| Default order | 72 — only used by `--gen-config` |
| Module source | `src/modules/break/break.c` |
| Detection source | — |

Prints exactly one empty line. It is the blank-line separator for a long structure:

```
Date & Time: 2026-01-01 09:00:00

OS: Linux 6.12.0
Shell: /bin/bash
```

This is the module with the fewest moving parts in the whole registry: **no key, no icon, no format
string, and no options at all**. The only keys it accepts are `type` and `condition`.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Everywhere | `src/modules/break/break.c` | Platform independent — no detection layer |

Because it has no detection layer, it is one of the few modules that work on *every* platform,
including the ones where a detection backend is only a stub. There is nothing to configure per
platform.

## Configuration

| Option | Type | Default | Description |
|---|---|---|---|
| `condition` | object | – | Prints the empty line only when the condition matches. See [Global](Modules-Global#condition-in-practice). |

That is the complete list. `key`, `keyColor`, `keyIcon`, `keyWidth`, `outputColor` and `format` are
**not** accepted; writing any of them is an unknown key (see [Pitfalls](#pitfalls)).

## Format string

None. The module does not call the format engine at all, and asking for its variable list fails:

```
$ fastfetch -h break-format
Error: Module 'Break' doesn't support output formatting
```

The message goes to stderr and the exit status is still 0. `format` is an unknown key here, so a
config that carries one prints a warning *and* the empty line.

## JSON output

There is no JSON representation:

```jsonc
{ "type": "Break", "error": "Unsupported for JSON format" }
```

`--structure break --format json` therefore produces an error object where a result would normally
be. In a full run the object still occupies its position in the top-level array, with `error` in
place of `result`.

## Examples

A blank line between two blocks:

```jsonc
{
  "modules": [
    { "type": "host" },
    { "type": "os" },
    { "type": "break" },
    { "type": "kernel" },
    { "type": "uptime" }
  ]
}
```

Several blank lines are several `break` entries — there is no `times` option:

```jsonc
{ "modules": [ { "type": "break" }, { "type": "break" } ] }
```

A blank line that only appears on Linux:

```jsonc
{ "type": "break", "condition": { "system": "Linux" } }
```

## Pitfalls

- **A `break` is a blank line, not a rule.** Use [Separator](Modules-Separator) when you want a
  visible horizontal line; `break` prints nothing but the newline.
- **One line, and only one.** `break` has no count option, so two blank lines need two entries.
- **The line is printed even when the module reports an unknown key.** The parse pass runs first, so
  `{ "type": "break", "key": "X" }` prints

  ```
  Break: Unknown JSON key key
  ```

  and then still prints its empty line. The message only appears when `display.showErrors` is true
  (see [Global](Modules-Global#globals-that-change-module-output)).
- **A `condition` that does not match prints nothing at all** — not even the empty line. Like every
  other module, the entry is skipped before it is dispatched, and the skip is silent even with
  `display.showErrors` enabled.
- **`break` cannot be hidden or renamed.** There is no key to hide with `key: " "` and no name to
  replace, so `--structure-disabled` is the only way to take it out of a structure that already
  contains it.
- **It always succeeds.** `ffPrintBreak()` returns `true` unconditionally, so a following
  `{ "condition": { "succeeded": true } }` is never suppressed by a `break`.
- **Order matters as much as anywhere else.** `break` occupies a slot in the structure like any
  other module, so a `break` placed first produces a leading blank line above the logo's first line.

## Implementation

The module is a three-line print function (`src/modules/break/break.c`):

```c
bool ffPrintBreak(FFBreakOptions* options) {
    ffLogoPrintLine();
    putchar('\n');
    return true;
}
```

`ffLogoPrintLine()` (`src/logo/logo.c`) is the same call every other module makes before printing its
key: it emits the next line of a side logo and pads to the logo's width, so the blank line lines up
with the rest of the output instead of overwriting part of an image logo. With `--logo none` it
produces nothing at all, which is why `-s break` is exactly one newline.

`ffParseBreakJsonObject()` accepts `type` and `condition` and rejects everything else.
`ffInitBreakOptions()` and `ffDestroyBreakOptions()` are both empty, and the registry entry carries
neither `formatArgs` nor `generateJsonResult` — that is the whole reason `-h break-format` and
`--format json` fail for this module.
