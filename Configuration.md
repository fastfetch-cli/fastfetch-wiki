This document provides comprehensive guidance on configuring Fastfetch to suit your needs.

## Basic Configuration

Fastfetch uses JSONC (JSON with comments) for configuration. The default configuration file is located at `~/.config/fastfetch/config.jsonc`

You can generate a default configuration file using:
```bash
fastfetch --gen-config
```

Using an editor with JSON schema support is highly suggested. If unsure, I suggest using [vscode](https://code.visualstudio.com/).

## Configuration Structure

The configuration file has the following main sections:

```jsonc
{
    "$schema": "https://github.com/fastfetch-cli/fastfetch/raw/dev/doc/json_schema.json", // Optional: Provides IDE intelligence
    "logo": { /* Logo configuration */ },
    "display": { /* Display settings */ },
    "modules": [ /* Modules to display */ ]
}
```

## Logo Configuration

Configure how the logo appears:

```jsonc
"logo": {
    "type": "auto",        // Logo type: auto, builtin, small, file, etc.
    "source": "arch",      // Built-in logo name or file path
    "width": 65,           // Width in characters (for image logos)
    "height": 35,          // Height in characters (for image logos)
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
        "type": "string"   // string, icon, both, or none
    },
    "bar": {
        "width": 10,       // Width of percentage bars
        "charElapsed": "■", // Character for elapsed portion
        "charTotal": "-"   // Character for total portion
    },
    "percent": {
        "type": 9,         // 1=number, 2=bar, 3=both, 9=colored number
        "color": {
            "green": "green",
            "yellow": "light_yellow",
            "red": "light_red"
        }
    }
}
```

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

## Format Strings

Many modules support custom format strings. For example:

```jsonc
{
    "type": "cpu",
    "format": "{name} ({cores-physical}C/{cores-logical}T) @ {freq-max}"
}
```

Use `fastfetch -h <module>-format` to see available format options for each module.

## Tips for Better Configuration

1. **Start with a preset**: Use `fastfetch --config examples/X` to start with a minimal configuration and build from there.

2. **Use JSON schema**: Adding the `$schema` line enables code completion and validation in editors like VSCode.

3. **Test individual modules**: Use the command line to test specific configurations before adding them to your file.
   ```
   fastfetch --structure memory --memory-percent-type 3
   ```

4. **Common display improvements**:
   - Set a consistent key width: `"key": { "width": 12 }`
   - Use bright colors: `"brightColor": true`
   - Adjust logo padding for better alignment: `"padding": { "left": 4 }`

### Documentation

Refer to: <https://github.com/fastfetch-cli/fastfetch/wiki/Json-Schema> ([truncated because of Github limitation](https://github.com/fastfetch-cli/fastfetch/issues/1587))

Generated using [json-schema-for-humans](https://github.com/coveooss/json-schema-for-humans) with

```
generate-schema-doc ~/fastfetch/doc/json_schema.json --config template_name=md Json-Schema.md
```

Also refer to `fastfetch --help` for more detailed explanation.

### Examples

`*.jsonc` in <https://github.com/fastfetch-cli/fastfetch/tree/dev/presets/examples>

You may test it with `fastfetch --config examples/x.jsonc`

### Notes

* Mixing command line flags with `config.jsonc` may / may not work. Generally, module command line option flags won't work when `config.jsonc` won't work. Other flags should work.
* Special charactors should be encoded as `\uXXXX` in JSON. Notably, `\e` or `\033` should be `\u001b`.