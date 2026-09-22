This document describes the color formats accepted by fastfetch wherever a color is expected: the `--color-*` options, the `color` properties of the JSONC config, and the `{#...}` placeholders in format strings.

## Supported formats

### ANSI Escape Sequences

A color encoding in ANSI escape sequence format. It will be inserted between "ESC[" and "m".
For more information about ANSI escape codes, visit: <https://en.wikipedia.org/wiki/ANSI_escape_code#SGR_(Select_Graphic_Rendition)_parameters>

```
35              # Magenta
38;5;38         # 38th color in the ANSI 256 color palette
4;92            # Bright green with underline
5;104           # Blinking text on a blue background (terminal support varies)
38;2;255;0;0    # RGB red (255,0,0)
1;38;5;220      # Bold orange (ANSI 256 color)
```

A value that is already a complete escape sequence is used as is: `\e[1;35m` and `1;35` are equivalent, and the trailing `m` is optional.

### Named Colors

ANSI named colors are also supported:

```
magenta                # Equivalent to `35`
bold_red               # Equivalent to `1;31`
underline_bright_green # Equivalent to `4;1;32`
```

* Supported named colors: `black`, `red`, `green`, `yellow`, `blue`, `magenta`, `cyan`, `white`, `default`, and `light_black`, `light_red`, `light_green`, `light_yellow`, `light_blue`, `light_magenta`, `light_cyan`, `light_white`
* Supported named prefixes: `reset_`, `bold_`, `bright_`, `dim_`, `italic_`, `underline_`, `blink_`, `inverse_`, `hidden_`, `strike_`
    * `bright_` is an alias for `bold_`; both expand to `1;`.
* Names are matched case-insensitively, and a prefix can be combined with a color name, or with each other: `bold_underline_red` is equivalent to `1;4;31`.
* There is no prefix for background colors, and `bg_blue` is rejected as an invalid color code. Write the escape sequence directly instead, for example `44` for a blue background.
* The keywords `keys`, `title`, `output` and `separator` are accepted as well. See [Special Keywords](#special-keywords).
* Run `fastfetch -h color` to print the list of named colors together with the ANSI code each one expands to.

### RGB Colors

`#RRGGBB` or `#RGB` (v2.42.0)
```
#FF0000              # Equivalent to `38;2;255;0;0` (red)
#F00                 # Same as #FF0000
```

### XTerm 256 Colors

`@<number>` (v2.57.0), where `<number>` is between 0 and 255
```
@196                   # Equivalent to `38;5;196` (red)
@34                    # Equivalent to `38;5;34` (green)
```

## Use in format strings or the JSONC config file

### Syntax

Use `{#color_code}` (e.g. `{#bold_red}`) to set color and `{#}` to reset. Note when using RGB Colors, two `#`s are required (`{##FF0000}`)

### Special Keywords

The following special keywords can be used to reference colors set by other options:
```
{#keys}                  # Uses the color set by `--color-keys`
{#title}                 # Uses the color set by `--color-title`
{#output}                # Uses the color set by `--color-output`
{#separator}             # Uses the color set by `--color-separator`
```
