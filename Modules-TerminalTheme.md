# TerminalTheme

> Print the current terminal theme (foreground and background colors)

| | |
|---|---|
| Module type | `terminaltheme` |
| Default order | 32 (only used by `--gen-config`) |
| Module source | `src/modules/terminaltheme/terminaltheme.c` |
| Detection source | `src/detection/terminaltheme/` |

Prints the terminal's foreground and background colours as `#RRGGBB`, plus whether the background
is dark.

```
Terminal Theme: #E5E5E5 (FG) - #000000 (BG) [Dark]
Terminal Theme: #FFFFFF (FG) - #1E1E1E (BG) [Dark]
```

The bracketed word at the end is the **background** type — the default line never prints the
foreground's. Use `{fg-type}` in a custom format if you need it.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| All platforms | `terminaltheme.c` | Escape-code query first, `COLORFGBG` as the fallback |

This is the only terminal-related module with no per-platform source file: `terminaltheme.c` sits
in the platform-independent part of the source list, and the two helpers it needs
(`ffGetTerminalResponse()`, `getenv()`) have per-platform implementations behind a common
interface. The module can still be compiled out of a build with the `FF_MODULE_DISABLE_TERMINALTHEME`
define, which removes both the module and the `display.brightColor` probe described below.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | `Terminal Theme` | Module key. A single space hides the key and the separator |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

There are no module-specific options. One environment variable changes the result:

| Variable | Effect |
|---|---|
| `COLORFGBG` | Fallback source, formatted as `<fg>;<bg>` with two palette indices. Only used when the escape-code query gets no answer |

## Format string

Run `fastfetch -h terminaltheme-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{fg-color}` | Foreground colour, `#RRGGBB` |
| `{fg-type}` | Foreground type, `Dark` or `Light` |
| `{bg-color}` | Background colour, `#RRGGBB` |
| `{bg-type}` | Background type, `Dark` or `Light` |

None of the four is marked `*` in the help output, so none is available in the key format.

```jsonc
{ "type": "terminaltheme", "format": "{fg-color}|{fg-type}|{bg-color}|{bg-type}" }
```

```
Terminal Theme: #FFFFFF|Light|#7F7F7F|Dark
```

## JSON output

```jsonc
[
    {
        "type": "TerminalTheme",
        "result": {
            "fg": { "r": 255, "g": 255, "b": 255, "dark": false },
            "bg": { "r": 127, "g": 127, "b": 127, "dark": true }
        }
    }
]
```

- `fg` and `bg` are objects with four keys each, in the order `r`, `g`, `b`, `dark`.
- `r` / `g` / `b` are numbers `0`–`255`; `dark` is a boolean.
- On failure the object carries `error` instead and has no `result`.
- Unlike the text output, the JSON keeps both `dark` flags, so the foreground type is available
  without a custom format.

## Examples

```jsonc
// A one-line summary of both types
{ "type": "terminaltheme", "format": "fg {fg-type} / bg {bg-type}" }
```

```jsonc
// Pick a logo variant from the background
{ "type": "terminaltheme", "format": "{bg-type}" }
```

## Pitfalls

- **"Dark" in the default line describes the background only.** The line ends with
  `[%s]` filled from `result.bg.dark`, so a light-background / dark-foreground terminal still
  prints `[Light]`. Both types are in the JSON and in the format variables.
- **`COLORFGBG` is the fallback, not the primary source.** The escape-code query
  (`OSC 10` / `OSC 11`) runs first and `COLORFGBG` is only consulted when it gets no parseable
  reply — so a terminal that answers the query ignores the variable completely.
- **`COLORFGBG` is quantised to 16 colours.** Its two numbers are indices into a fixed palette, and
  the values in that palette are the classic X11 ones (`1` is `#CD0000`, not `#FF0000`; `7` is
  `#E5E5E5`). An index outside `0`–`15` maps to black, and a value that does not parse as exactly
  two `%d` separated by `;` is rejected outright.
- **A missing `COLORFGBG` and an unresponsive terminal means no output.** The module reports
  `Failed to detect terminal theme` through `ffPrintError()`, which is silent unless
  `display.showErrors` is `true` — the module then simply vanishes, exactly like the other
  terminal modules.
- **The "dark" test is a luma threshold, not a perceptual one.** It is
  `r * 299 + g * 587 + b * 114 < 128000`, i.e. Rec. 601 luma below 128 on a 0–255 scale. A
  mid-grey background around `#808080` sits right at the boundary and can land either way.
- **The escape query costs up to a second.** `ffGetTerminalResponse()` waits up to
  `FF_IO_TERM_RESP_WAIT_MS` (1000 ms) for the reply, so a terminal that does not implement
  `OSC 10` / `OSC 11` delays the module by that much before falling back to `COLORFGBG`. The
  source records that Windows Terminal strips all `\e` from its output, which is one such case.
- **The same detection also controls `display.brightColor`.** `ffOptionsInitDisplay()` probes the
  theme with `forceEnv = true` — the environment path only, deliberately skipping the escape query
  "for performance" — and sets `brightColor` to `false` when the background is light. That happens
  at option-init time, whether or not the `terminaltheme` module is enabled, so exporting
  `COLORFGBG` changes the colours of unrelated modules.
- **This module is not cached.** Each call re-runs the detection, so under `--dynamic-interval` it
  re-queries the terminal on every round.

## Implementation

`ffDetectTerminalTheme()` fills an `FFTerminalThemeResult` — two `FFTerminalThemeColor` values
(`uint8_t r, g, b` plus a `bool dark`) — and then computes both `dark` flags. It is the only
detector in this area that takes a `forceEnv` argument:

```c
if (!forceEnv && detectByEscapeCode(result)) return true;
return detectByEnv(result);
```

`ffPrintTerminalTheme()` formats the default line as
`#%02X%02X%02X (FG) - #%02X%02X%02X (BG) [%s]`, or pre-renders the two `#RRGGBB` strings and the
two type strings for the format engine. `ffGenerateTerminalThemeJsonResult()` writes the two
nested objects.

### Escape-code path

Two queries are sent back to back — `\e]10;?\e\\` for the foreground and `\e]11;?\e\\` for the
background — and the replies are parsed as `rgb:RRRR/GGGG/BBBB` with
`%*[^0-9]10;rgb:%x/%x/%x … %*[^0-9]11;rgb:%x/%x/%x`. The leading `%*[^0-9]` is what makes the parse
tolerant of the escape sequence itself being present or stripped. Components above `0x100` — a
terminal answering with 16-bit channels — make all three components of that colour be divided by
`0x100`.

### Environment path

`COLORFGBG` is split with `sscanf(color, "%d;%d", &f, &g)`, and each number goes through
`fgbgToColor()`, a 16-entry switch over the classic ANSI palette (`0` black, `1` `#CD0000`,
`2` `#00CD00`, `3` `#CDCD00`, `4` `#0000EE`, `5` `#CD00CD`, `6` `#00CDCD`, `7` `#E5E5E5`, then the
bright variants `8`–`15`, `8` being `#7F7F7F` and `15` `#FFFFFF`). Anything outside that range
becomes black.
