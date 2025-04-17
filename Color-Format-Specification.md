All color settings use the following format:

The `<color>` parameter must be a color encoding in ANSI escape sequence format, which is inserted between "ESC[" and "m".
For more information about ANSI escape codes, visit: <https://en.wikipedia.org/wiki/ANSI_escape_code#SGR_(Select_Graphic_Rendition)_parameters>

## Examples

### ANSI Escape Sequences
```
35              # Magenta
38;5;38         # 38th color in the ANSI 256 color palette
4;92            # Bright green with underline
5;104           # Blinking text on a blue background (terminal support varies)
38;2;255;0;0    # RGB red (255,0,0)
1;38;5;220      # Bold orange (ANSI 256 color)
```

### Named Colors
ANSI named colors are also supported:
```
magenta                # Equivalent to `35`
underline_bright_green # Equivalent to `4;92`
bold_red               # Equivalent to `1;31`
bg_blue                # Equivalent to `44` (blue background)
```

### RGB Colors
#RRGGBB or #RGB (v2.42.0)
```
#FF0000                # Equivalent to `38:2;255;0;0` (red)
#F00                   # Same as #FF0000
```

## Use in `--<module>-format` or JSONC config file

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