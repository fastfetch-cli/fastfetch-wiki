## JSONC based configuration

Fastfetch uses JSONC (JSON with comments) to store configuration. It should be located in `~/.config/fastfetch`, namely `config.jsonc`. You may create it with

```jsonc
// ~/.config/fastfetch/config.jsonc
{
    "$schema": "https://github.com/fastfetch-cli/fastfetch/raw/dev/doc/json_schema.json"
}
```

Using an editor with JSON schema support is highly suggested. If unsure, I suggest using [vscode](https://code.visualstudio.com/).

### Logo options

Specify logo options in an object with key of `logo`:

```jsonc
{
    "logo": {
        "type": "iterm",
        "source": "/path/to/logo.png",
        "width": 34,
        "height": 15
    }
}
```

See https://github.com/fastfetch-cli/fastfetch/wiki/Logo-options for detail

*`"logo": null` is equivalent to `"logo": { "type": "none" }`.
*`"logo": "/path/to/logo.png"` is equivalent to `"logo": { "source": "/path/to/logo.png" }`.

### General options
