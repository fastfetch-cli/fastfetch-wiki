## JSON based configuration

Fastfetch uses JSONC (JSON with comments) to store configuration. It should be located in `~/.config/fastfetch`, named `config.jsonc` (NOT ~`config.json`~). You may create it with `fastfetch --gen-config`. A simplest JSONC file should be like

```jsonc
// ~/.config/fastfetch/config.jsonc
{
    "$schema": "https://github.com/fastfetch-cli/fastfetch/raw/dev/doc/json_schema.json"
}
```

Using an editor with JSON schema support is highly suggested. If unsure, I suggest using [vscode](https://code.visualstudio.com/).

### Migrate `fastfetch` flags to JSONC config file

```shell
# Make sure that `~/.config/fastfetch/config.jsonc` doesn't exist
fastfetch --whatever-flags --gen-config
```

### Why use JSON?

We had command line, flag based configuration for long time. Why bother introducing a new config format?

1. JSON is standard. Most IDEs have builtin support of syntax highlighting, validation and formatting, without additional requirements.
1. JSON is structure based. It's easier to read with proper indentation.
1. With JSON schema, you get on-the-fly IDE intelligence when typing.
1. JSON supports array. Print one module mutiple times without hacking the command line flags.

<img width="628" alt="image" src="https://github.com/fastfetch-cli/fastfetch/assets/6134068/60159686-9b9f-4500-b87b-b5181279f7f6">

### Documentation

Refer to: https://github.com/fastfetch-cli/fastfetch/wiki/Json-Schema

Generated using [json-schema-for-humans](https://github.com/coveooss/json-schema-for-humans) with

```
generate-schema-doc ~/fastfetch/doc/json_schema.json --config template_name=md Json-Schema.md
```

Also refer to `fastfetch --help` for more detailed explanation.

### Examples

`*.jsonc` in <https://github.com/fastfetch-cli/fastfetch/tree/dev/presets/examples>

You may test it with `fastfetch --config examples/x.jsonc`

### Note

* When `config.jsonc` is found, `config.conf` will be ignored
* Mixing command line flags with `config.jsonc` may / may not work. Generally, module command line option flags won't work when `config.jsonc` won't work. Other flags should work.