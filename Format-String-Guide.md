## Basic Syntax

A format string is a text string that contains placeholders for values. Each placeholder begins with `{`, contains either the name or numeric index of a value, and ends with `}`.

For example:
```
"Values: {1} ({2})" with values "First" and "My second val" produces "Values: First (My second val)"
```

Format strings can contain placeholders in any order, and the same placeholder may appear multiple times.

When setting a format string from the command line that contains spaces, surround it with double quotes:
```
fastfetch --title-format "Hello, {user-name}"
```

## Named Arguments

Instead of numeric placeholders like `{1}`, you can use named arguments that identify values by name:
```
"--title-format '{user-name-colored}{at-symbol-colored}{host-name-colored}'"
```

This is equivalent to using numeric placeholders, but far more readable and resilient. See module-specific help for available named arguments:
```
fastfetch -h title-format
```

> **Always prefer named arguments over numeric placeholders.** Numeric placeholder positions can change between releases (e.g., when arguments are reordered), which is a breaking change for any config that relies on them. Named arguments like `{user-name}` remain stable regardless of ordering.

## String Manipulation

### Truncation

You can specify a truncation length using the syntax '{arg:trunc-length}':
```
"--title-format '{user-name:5}'" → truncates user name to 5 characters
```

If 'trunc-length' is negative, an ellipsis (…) will be appended when truncated.

Note: String length is counted in raw bytes; multi-byte Unicode characters and ANSI escape codes may not be counted as expected.

### Padding

Use '<', '>' or '|' instead of ':' to set left, right or center padding:
```
"{user-name<20}" →   left-aligned with spaces: "<user-name>         "
"{user-name>20}" →  right-aligned with spaces: "         <user-name>"
"{user-name|20}" → center-aligned with spaces: "    <user-name>     " # Added in v2.64.0
```

### Slicing

Use '{~startIndex,endIndex}' to slice a string:
```
"{~0,5}"  → first five characters
"{~-5,}"  → last five characters
"{~2,-2}" → from third character to second-to-last character
```

Negative indices count backward from the end of the string. If an index is omitted, 0 is used.

## Variable References

### Constants and Environment Variables

You can reference constants and environment variables:
```
"{$NUM}"     → reference a constant defined in `display.constants`
"{$ENV_VAR}" → reference an environment variable
```

### Automatic Indexing

If a placeholder has no index or name (i.e., `{}`), an internal counter automatically assigns the next sequential numeric index:
```
"Values: {} ({})" → equivalent to "Values: {1} ({2})"
```

Note that this counter only increments for empty placeholders:
```
"{2} {} {}" → second value, then first value, then second value again
```

For the same reasons as above, prefer named arguments over relying on automatic indexing.

## Special Formatting

### Escaping Curly Braces

A double open curly brace ("{{") will be printed as a single open curly brace ('{') and not treated as a placeholder.

### Conditional Content

To conditionally print content only when a variable is set:
```
"{?2} Second value: {2}{?}" → prints only if value 2 is set
```

To conditionally print content only when a variable is NOT set:
```
"{/2}Value not available{/}" → prints only if value 2 is NOT set
```

Example combining both:
```
"{?2}{2}{?}{/2}Second value fallback{/}"
```

### Terminating Formatting

To terminate formatting at any point, use "{-}".

## Color Formatting

To apply color to text, start a placeholder with '#' followed by terminal color codes:
```
"{#4;35}Colored Text{#}" → pink and underlined text
```

The escape sequence "\033[" at the start and 'm' at the end are automatically added.

"{#}" is equivalent to "{#0}" and resets all formatting to normal.

Named formats are also supported:
```
"{#underline_magenta}Colored Text{#}"
```

See `fastfetch -h color` for details about supported color codes.

## Empty Values

If a format string evaluates to an empty value, the entire line will be omitted from the output.

This can be used to disable specific outputs:
```
"--host-format ' '" → disables host output
```

Note that using an empty string (e.g., "--host-format ''") would be treated as not set, and the built-in format would be used instead.

## Fixed Values

Format strings can also be used to set fixed values—simply use a string without any placeholders:
```
"--custom-format Preferred"
```

---

## Scripting Support (Experimental)

For advanced custom formatting and cross-module data manipulation, Fastfetch supports embedding **Lua** or **QuickJS (JavaScript)** scripts directly within format strings. Supported in 2.64 or later.

### Lua Scripts
Prefix your format string with `lua:` to execute Lua code.

* **Return Values:** A `return` statement is required to pass the final string result back to the Fastfetch module. If `nil` is returned (or omitted implicitly), the entire module output is skipped.
* **Parameters:** Module-specific variables are passed via variable arguments `(...)`. You can assign them to local variables for better readability.
* **State Sharing:** The Lua interpreter instance is **shared across all modules**. This allows you to store data in one module and access it in another.
* **Debugging:** A `json_encode(table, is_pretty)` helper function is injected into the Lua API to easily dump and inspect available variables.
* **Requirements:** Supports Lua 5.3 to 5.5 (Lua 5.1 and LuaJIT are **not** supported). The Lua version is auto-detected at build time and can be verified using `fastfetch --list-features`.

**Basic Usage:**
```jsonc
{
    "type": "title",
    "format": "lua:local args = ...; return string.format('Hello %s@%s', args.userName, args.hostName)" // prints `Hello {user-name}@{host-name}`
}
```

**Cross-Module Data Sharing:**
```jsonc
[
    // 1. Store the shell object globally (no "return", so this module's output is skipped)
    { "type": "shell", "format": "lua:shell = ..." },

    // 2. Access the stored object in a subsequent module
    { "type": "terminal", "format": "lua:return shell.prettyName .. ' in ' .. (...).prettyName" } // prints `{shell-name} in {terminal-name}`
]
```

**Debugging Available Variables:**
```jsonc
{
    "type": "cpu",
    "format": "lua:return json_encode(..., true)"
}
```

Users can use the line-break syntax of JSON5 to break long lines. [Example](https://github.com/fastfetch-cli/fastfetch/discussions/2379#discussioncomment-17194638)

### QuickJS (JavaScript) Scripts
As an alternative to Lua, you can execute JavaScript by prefixing your format string with `qjs:`. 

* **Return Values:** No explicit `return` statement is needed; the final result is simply the evaluated value of the script expression.
* **Parameters:** Module-specific variables are passed via the `this` context object. Usage is mostly the same as Lua, but utilizing JavaScript syntax.
* **Requirements:** Requires the Fastfetch binary to be built with [quickjs-ng v0.15.0](https://github.com/quickjs-ng/quickjs/releases/tag/v0.15.0) or newer.

**Basic Usage:**
```json5
{
    "type": "title",
    "format": "qjs:`Hello ${this.userName}@${this.hostName}`"
}
```
Access global object via `globalThis`; debug available variables via `JSON.stringify(this)`