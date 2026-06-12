## Basic Syntax

A format string contains placeholders for dynamic values. Each placeholder starts with `{`, contains a value name or index, and ends with `}`.

For example:
```jsonc
{
  "type": "title",
  "format": "{user-name}@{host-name}" // Evaluates to "user@my-host" given the values "user" and "my-host"
}
```

See the module-specific help for available named arguments:
```bash
$ fastfetch -h title-format
```

## Numeric (Index-Based) Arguments

**DEPRECATED: Always prefer named arguments over numeric placeholders.**

Numeric placeholder positions can change between releases (e.g., if arguments are reordered), which introduces breaking changes for any configuration relying on them. Named arguments like `{user-name}` remain stable regardless of argument ordering.

## String Manipulation

### Truncation

You can specify a truncation length using the `{arg:length}` syntax:
```text
"{user-name:5}" → truncates the username to 5 characters
```

If the length is negative, an ellipsis (`…`) is appended to the truncated string.

*Note: String length is measured in raw bytes. Multi-byte Unicode characters and ANSI escape codes may not be counted as expected.*

### Padding

Use `<`, `>`, or `|` instead of `:` to apply left, right, or center padding:
```jsonc
"{user-name<20}" →  left-aligned with spaces: "username            "
"{user-name>20}" → right-aligned with spaces: "            username"
"{user-name|20}" → center-aligned with spaces: "      username      " // Added in v2.64.0
```

*Note: String length is measured in raw bytes.*

### Slicing

Use `` `{variable~startIndex,endIndex}` `` to slice a string:
```text
"{user-name~0,5}"  → first five characters
"{user-name~-5,}"  → last five characters
"{user-name~2,-2}" → from the third character to the second-to-last character
```

Negative indices count backward from the end of the string. Omitted indices default to `0`.

*Note: String length is measured in raw bytes.*

## Variable References

### Constants and Environment Variables

You can reference constants and environment variables using the `$` prefix:
```text
"{$NUM}"     → references a constant defined in `display.constants`
"{$ENV_VAR}" → references an environment variable
```

## Special Formatting

### Escaping Curly Braces

A double open curly brace (`{{`) is printed as a single open curly brace (`{`) and is not treated as a placeholder.

### Conditional Content

To conditionally print content only when a variable is set (not empty):
```text
"{?user-name}Username: {user-name}{?}" → Prints only if `{user-name}` is set
```

To conditionally print content only when a variable is NOT set (empty):
```text
"{/user-name}Username not available{/}" → Prints only if `{user-name}` is NOT set
```

Example combining both:
```text
"{?user-name}{user-name}{?}{/user-name}Username fallback{/}"
```

### Terminating Formatting

To terminate formatting at any point, use `{-}`.

## Color Formatting

To apply color to text, start a placeholder with `#` followed by terminal color codes:
```text
"{#4;35}Colored Text{#}" → underlined magenta (pink) text
```

The ANSI escape sequence `\e[` at the start and `m` at the end are automatically appended.

`{#}` is equivalent to `{#0}` and resets all formatting to default.

Named color formats are also supported:
```text
"{#underline_magenta}Colored Text{#}"
```

See `fastfetch -h color` for details on supported color codes.

## Empty Values

If a format string evaluates to whitespace, the entire line is omitted from the output.

This can be used to disable specific outputs:
```jsonc
{
  "type": "host",
  "key": " " // Disables the key for the host module
}
```

*Note: Using an empty string (`""`) is treated as "unset", causing the built-in default format to be used instead.*

---

## Scripting Support (Experimental)

For advanced custom formatting and cross-module data manipulation, Fastfetch supports embedding **Lua** or **QuickJS (JavaScript)** scripts directly within format strings. This feature requires Fastfetch v2.64.0 or later.

### Lua Scripts
Prefix your format string with `lua:` to execute Lua code.

* **Return Values:** A `return` statement is required to pass the final string result back to the Fastfetch module. If `nil` is returned (or implicitly omitted), the entire module's output is skipped.
* **Parameters:** Module-specific variables are passed via variable arguments (`...`). You can assign them to local variables for better readability.
* **State Sharing:** The Lua interpreter instance is **shared across all modules**, allowing you to store data in one module and access it in another.
* **Debugging:** A `json_encode(table, is_pretty)` helper function is injected into the Lua environment to easily dump and inspect available variables.
* **Requirements:** Supports Lua 5.3 through 5.5 (Lua 5.1 and LuaJIT are **not** supported). The Lua version is auto-detected at build time and can be verified using `fastfetch --list-features`.

**Basic Usage:**
```jsonc
{
    "type": "title",
    "format": "lua:local args = ...; return string.format('Hello %s@%s', args.userName, args.hostName)" // Prints `Hello user@host`
}
```

**Cross-Module Data Sharing:**
```jsonc
[
    // 1. Store the shell object globally (no "return", so this module's output is skipped)
    { "type": "shell", "format": "lua:shell = ..." },

    // 2. Access the stored object in a subsequent module
    { "type": "terminal", "format": "lua:return shell.prettyName .. ' in ' .. (...).prettyName" } // Prints `Zsh in Alacritty`
]
```

**Debugging Available Variables:**
```jsonc
{
    "type": "cpu",
    "format": "lua:return json_encode(..., true)"
}
```

You can use JSON5's line-continuation syntax (backslashes) to break up long script lines. [Example](https://github.com/fastfetch-cli/fastfetch/discussions/2379#discussioncomment-17194638)

### QuickJS (JavaScript) Scripts
As an alternative to Lua, you can execute JavaScript by prefixing your format string with `qjs:`. 

* **Return Values:** No explicit `return` statement is needed; the final result is simply the evaluated value of the script's last expression.
* **Parameters:** Module-specific variables are passed via the `this` context object. Usage is conceptually similar to Lua but utilizes JavaScript syntax.
* **Requirements:** Requires the Fastfetch binary to be built with [quickjs-ng v0.15.0](https://github.com/quickjs-ng/quickjs/releases/tag/v0.15.0) or newer.

**Basic Usage:**
```json5
{
    "type": "title",
    "format": "qjs:`Hello ${this.userName}@${this.hostName}`"
}
```
You can access the global object via `globalThis` and debug available variables using `JSON.stringify(this)`.