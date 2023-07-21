## JSONC based configuration

Fastfetch uses JSONC (JSON with comments) to store configuration. It should be located in `~/.config/fastfetch`, namely `config.jsonc`. You may create it with

```jsonc
// ~/.config/fastfetch/config.jsonc
{
    "$schema": "https://github.com/fastfetch-cli/fastfetch/raw/dev/doc/json_schema.json"
}
```

Using an editor with JSON schema support is highly suggested. If unsure, I suggest using [vscode](https://code.visualstudio.com/).

### Documentation

Generated using [json-schema-for-humans](https://github.com/coveooss/json-schema-for-humans) with

```
generate-schema-doc ~/fastfetch/doc/json_schema.json --config template_name=md Json-Schema.md
```
