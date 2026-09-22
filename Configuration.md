This document provides comprehensive guidance on configuring Fastfetch to suit your needs.

## Basic Configuration

Fastfetch uses JSONC (JSON with comments) for configuration. The default configuration file is located at `~/.config/fastfetch/config.jsonc`

You can generate a configuration file using:
```sh
fastfetch --gen-config
```
Run in a terminal, this opens an interactive configuration UI. When stdin or stdout is not a TTY, or `$NO_COLOR` is set, it falls back to writing a minimal configuration non-interactively.

There is also an online configuration generator: https://fastfetch-cli.github.io/fastfetch-config/

Using an editor with [JSON schema](https://json-schema.org/) support is highly suggested. If unsure, I suggest using [vscode](https://code.visualstudio.com/) or [helix](https://helix-editor.com/).

## Configuration Structure

The configuration file has the following main sections:

```jsonc
{
    "$schema": "https://github.com/fastfetch-cli/fastfetch/raw/dev/doc/json_schema.json", // Optional: Provides IDE intelligence
    "general": { /* General settings */ },
    "logo": { /* Logo configuration */ },
    "display": { /* Display settings */ },
    "modules": [ /* Modules to display */ ]
}
```

An unknown key in any section is an error, and fastfetch reports which one it was.

## General Configuration

Settings that are not about the logo, the display or a single module:

```jsonc
"general": {
    "thread": true,            // Use separate threads for HTTP requests
    "processingTimeout": 5000, // Timeout in ms when waiting for child processes; -1 disables it
    "detectVersion": true,     // Whether to detect component versions. Mainly for benchmarking
    "playerName": "",          // Player to use for the Media and Player modules. Linux only
    "dsForceDrm": false        // Force DRM for display detection. Linux only
}
```

`dsForceDrm` also accepts the string `"sysfs-only"`. See [Display Detection Methods](Display-Detection-Methods) for the detection order it controls.

## Logo Configuration

Configure how the logo appears:

```jsonc
"logo": {
    "type": "auto",        // Logo type: auto, builtin, small, file, etc.
    "source": "arch",      // Built-in logo name or file path
    "width": 65,           // Width in characters
    "height": 35,          // Height in characters
    "padding": {
        "top": 0,          // Top padding
        "left": 0,         // Left padding
        "right": 2         // Right padding
    },
    "color": {             // Override logo colors
        "1": "blue",
        "2": "green"
    }
}
```

`"logo": null` disables the logo entirely, and `"logo": "arch"` is a shorthand for setting only `source`. See [Logo Options](Logo-options) for the full list of properties, including the image logo cache and animation settings.

## Display Configuration

Control how information is displayed:

```jsonc
"display": {
    "separator": ": ",     // Separator between keys and values
    "color": {
        "keys": "blue",    // Key color
        "title": "red"     // Title color
    },
    "key": {
        "width": 12,       // Aligns keys to this width
        "type": "string"   // See below
    },
    "bar": {
        "width": 10,       // Width of percentage bars
        "char": {
            "elapsed": "■", // Character for elapsed portion
            "total": "-",    // Character for total portion
        },
    },
    "percent": {
        "type": 9,         // See below
        "color": {
            "green": "green",
            "yellow": "light_yellow",
            "red": "light_red"
        }
    }
}
```

### `display.key.type`

| Value | Description |
|-------|-------------|
| `string` | The default. Show the module name |
| `icon` | Show a built-in icon instead of the module name |
| `both` | Show the icon and the name, with a space between them. Alias of `both-1` |
| `both-0` | Show the icon and the name with no space between them |
| `both-1` … `both-4` | Show the icon and the name with that many spaces between them |
| `none` | Show neither |

### `display.percent.type`

A bitmask, written either as a number or as an array of style flags:

| Flag | Bit | Description |
|------|-----|-------------|
| `num` | 1 | Show the percentage number |
| `bar` | 2 | Show a bar |
| `hide-others` | 4 | Hide the values that the other bits did not select |
| `num-color` | 8 | Color the percentage number by the thresholds below, instead of drawing a colored bar |
| `bar-monochrome` | 16 | Draw the bar in a single color instead of coloring it by the thresholds |

Common numbers, which are just combinations of the bits above:

| Number | Flags | Description |
|--------|-------|-------------|
| `0` | — | Use the global setting |
| `1` | `num` | Show the percentage number |
| `2` | `bar` | Show a bar colored by the thresholds |
| `3` | `num` `bar` | Show both |
| `6` | `bar` `hide-others` | Show only the bar |
| `9` | `num` `num-color` | Show a colored number. The default |
| `10` | `bar` `num-color` | Show a monochrome bar |

The other `display` sections — `size`, `temp`, `freq`, `duration`, `fraction`, `common`, `constants`, `stat`, `pipe`, `showErrors`, `brightColor`, `disableLinewrap`, `hideCursor` and `noBuffer` — are documented in the [JSON schema](https://github.com/fastfetch-cli/fastfetch-wiki/blob/master/Json-Schema.md#schema) and in `fastfetch --help`.

## Module Configuration

Specify which modules to display and their configuration:

```jsonc
"modules": [
    "title",
    "separator",
    {
        "type": "os",
        "key": "OS",
        "keyColor": "blue",
        "format": "{name} {version}"
    },
    {
        "type": "kernel",
        "key": "Kernel"
    },
    {
        "type": "memory",
        "key": "Memory",
        "percent": {
            "type": 3,     // Show both percentage number and bar
            "green": 30,   // Values below 30% in green
            "yellow": 70   // 30-70% in yellow, >70% in red
        }
    }
]
```

A module can be given as a plain string, which uses its default configuration, or as an object with a `type`. A module-level `percent.type` of `0` — which is the default — uses the global `display.percent.type` instead.

Modules can only be configured in the JSON config. The `--<module>-<option>` command line options were removed in v2.52.0.

## Format Strings

Many modules support custom format strings. For example:

```jsonc
{
    "type": "cpu",
    "format": "{name} ({cores-physical}C/{cores-logical}T) @ {freq-max}"
}
```

Use `fastfetch -h <module>-format` to see available format options for each module. See [Format String Guide](Format-String-Guide) for the syntax.

## Tips for Better Configuration

1. **Start with a preset**: Use `fastfetch --config examples/X` to start with a minimal configuration and build from there.

2. **Use JSON schema**: Adding the `$schema` line enables code completion and validation in editors like VSCode.

## Documentation

Refer to: <https://github.com/fastfetch-cli/fastfetch-wiki/blob/master/Json-Schema.md#schema> ([Different repo is used because of Github Wiki limitation](https://github.com/fastfetch-cli/fastfetch/issues/1587))

Generated using [json-schema-for-humans](https://github.com/coveooss/json-schema-for-humans) with

```
generate-schema-doc ~/fastfetch/doc/json_schema.json --config template_name=md Json-Schema.md
```

Also refer to `fastfetch --help` for more detailed explanation.

## Examples

The `presets/` directory in the repository contains ready-made config files you can copy and adapt:

* `presets/examples/` — small examples, each demonstrating one feature. Browse them at <https://github.com/fastfetch-cli/fastfetch/tree/dev/presets/examples>.
* `presets/` — full-featured presets such as `all`, `neofetch`, `screenfetch`, `archey` and `paleofetch`.

Run `fastfetch --list-presets` to see the presets that fastfetch can actually find on your system:

```bash
$ fastfetch --list-presets
```

A preset is looked up by name in the preset search paths, so it works from any directory:

```bash
$ fastfetch --config examples/18.jsonc
```

Note that this is a preset lookup, not a path relative to the current directory — `--config presets/examples/18.jsonc` is not guaranteed to work.

## Notes

* Special characters should be encoded as `\uXXXX` in JSON. Notably, `\e` or `\033` should be `\u001b`.
