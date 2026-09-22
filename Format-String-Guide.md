This document describes the format string syntax used by the `format` property of a module, and by the `key` property when it is used as a format string.

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

A placeholder that cannot be resolved, such as a misspelled argument name or an out-of-range index, is printed verbatim rather than being dropped, which makes it easy to spot. `fastfetch --show-errors` does not report them.

## Numeric/Index-Based (starts from `1`) Arguments

**DEPRECATED**: Always prefer named arguments over numeric placeholders. Numeric placeholder positions can change between releases (for example if arguments are reordered), which introduces breaking changes for any configuration relying on them. Named arguments such as `{user-name}` remain stable regardless of argument ordering.

`{1}@{2}` or `{}@{}` (indices are implicitly increased, starts from 1)
> Because of historical reasons, `{0}` is the same as `{}`

## String Manipulation

### Truncation

You can specify a truncation length using the `{arg:length}` syntax:
```text
"{user-name:5}" → truncates the username to 5 characters
```

If the length is negative, an ellipsis (`…`) is appended to the truncated string.

*Note: the length is measured in raw bytes, not in characters or display columns, so a multi-byte character counts as several and can be cut in half. A color escape sequence at the start of the value, and the reset at the end of it, are not counted; one in the middle of the value is.*

### Padding

Use `<`, `>`, or `|` instead of `:` to apply left, right, or center padding:
```jsonc
"{user-name<20}" →  left-aligned with spaces: "username            "
"{user-name>20}" → right-aligned with spaces: "            username"
"{user-name|20}" → center-aligned with spaces: "      username      " // Added in v2.64.0
```

*Note: String length is measured in raw bytes, as above.*

### Slicing

Use `{variable~startIndex,endIndex}` to slice a string:
```text
"{user-name~0,3}"   → characters 0 to 2: "car"
"{user-name~1}"     → from character 1 to the end: "arter"
"{user-name~2,4}"   → characters 2 to 3: "rt"
"{user-name~-3,-1}" → characters 3 to 4: "te"
```

Negative indices count backward from the end of the string. The start index is inclusive and the end index is exclusive, an omitted start index defaults to `0`, and an end index past the end of the string is clamped to it.

*Note: String length is measured in raw bytes, as above.*

**Note:** an omitted *end* index does not mean "up to the end of the string". `"{user-name~2,}"` and `"{user-name~-5,}"` both evaluate to an empty string, because the missing end index is read as `0` and `0` is never greater than the start index. Give the end index explicitly, or drop the comma to slice to the end of the string (`"{user-name~2}"`).

## Variable References

### Constants and Environment Variables

You can reference constants and environment variables using the `$` prefix:
```text
"{$1}"       → the first entry of `display.constants`
"{$-1}"      → the last entry of `display.constants`
"{$ENV_VAR}" → an environment variable
```

* Constants are numbered from `1`, and a negative index counts backward from the end.
* `{$0}` and any index outside the list are printed verbatim.

## Special Formatting

### Escaping Curly Braces

A double open curly brace (`{{`) is printed as a single open curly brace (`{`) and is not treated as a placeholder. There is no escape for a closing brace; a `}` that does not close a placeholder is printed as is.

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

To terminate formatting at any point, use `{-}`. Everything after it in the format string is discarded.

## Color Formatting

To apply color to text, start a placeholder with `#` followed by terminal color codes:
```text
"{#4;35}Colored Text{#}" → underlined magenta (pink) text
```

The ANSI escape sequence `\e[` at the start and `m` at the end are automatically appended.

`{#}` resets all formatting to default, as does `{#0}`.

Named color formats are also supported:
```text
"{#underline_magenta}Colored Text{#}"
```

See [Color Format Specification](https://github.com/fastfetch-cli/fastfetch/wiki/Color-Format-Specification) for the supported color codes.

## Hiding a Key

Setting `key` to exactly one space hides the key of a module, so that only its value is printed:

```jsonc
{
  "type": "host",
  "key": " " // Hides the key for the host module
}
```

* The value must be exactly one space. Any other value is printed as the key, including two spaces (`"  "`).
* An empty key (`""`) is treated as "unset", so the module's built-in key is used instead.
* The same applies to `format`: an empty format string (`""`) is treated as "unset", so the module's built-in format is used instead.
* A format string that evaluates to nothing still prints the key and the separator, followed by an empty value. It does not remove the line.

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
