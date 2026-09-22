# Editor

> Print information about the default editor (`$VISUAL` or `$EDITOR`)

| | |
|---|---|
| Module type | `editor` |
| Default order | 16 (only used by `--gen-config`) |
| Module source | `src/modules/editor/editor.c` |
| Detection source | `src/detection/editor/` |

Prints the editor named by `$VISUAL` (preferred) or `$EDITOR`, together with its version when one
could be determined. The version is printed only when the resolved executable name is known.

Both variables hold a shell command line rather than a bare name, so only the first word of the
value is used: `EDITOR="code -w"` reports `code`. A value that does not name a runnable program is
an error, not something the module echoes back.

```
Editor: nano 9.2
Editor: nvim 0.10.2
Editor: vim
```

## Platform support

The module is **platform-independent**. `src/detection/editor/editor.c` is a single file listed in
the common source list (`CMakeLists.txt:484`), there is no `editor_nosupport.c`, and no platform
block in `CMakeLists.txt` mentions `detection/editor` — the same code runs everywhere.

| Platform | Implementation | Notes |
|---|---|---|
| All ten supported platforms | `editor.c` | Reads `$VISUAL` / `$EDITOR`, resolves the path, optionally runs the binary |

The only platform-conditional lines in the file are the path separator (`/` versus `\`), the
stripping of a trailing `.exe` on Windows, and a snap carve-out on Linux (see below).

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works, `""` prints none. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

There are no module-specific keys. The editor to report is chosen by the environment, not by
configuration — there is no `editor` or `command` option.

## Format string

Run `fastfetch -h editor-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{type}` | `Visual` when `$VISUAL` was used, `Editor` when `$EDITOR` was |
| `{name}` | First word of the environment value — the name that was looked up |
| `{exe-name}` | Basename of the resolved path |
| `{path}` | Full resolved path of the editor |
| `{version}` | Version string, empty when it could not be determined |

## JSON output

```jsonc
{
    "type": "Editor",
    "result": {
        "type": "Visual",
        "name": "nano",
        "path": "/usr/bin/nano",
        "exe": "nano",
        "version": "9.2"
    }
}
```

Note the key order and naming: the resolved basename is `exe` here but `{exe-name}` in a format
string, and `name` is the first word of the environment value, not the editor's display name.

## Examples

```jsonc
{ "type": "editor", "format": "{type}: {exe-name} {version}" }
```

```jsonc
{ "type": "editor", "key": "EDITOR", "format": "{path}" }
```

## Pitfalls

- **The value is split on whitespace, so a path containing a space cannot be used.** Only the
  first word is taken as the program name. That is what makes `EDITOR="code -w"` work, but it also
  means a value such as

  ```
  EDITOR='/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code'
  ```

  is cut down to `/Applications/Visual`, which then fails `realpath()`. Point the variable at a
  launcher on `$PATH` (`code`) or at a path without spaces. Quoting inside the variable does not
  help — the split happens before anything is looked up, and the quotes are not stripped.
- **A value that does not name an executable is an error.** If the name is not on `$PATH`, if
  `realpath()` fails, or if the resolved path has no basename, detection fails and the message names
  the variable that was actually read:

  ```
  EDITOR=bogus   ->  Editor: $EDITOR does not point to an executable
  VISUAL='   '   ->  Editor: $VISUAL does not point to an executable
  ```

  An absolute `$EDITOR` is copied into `path` before it is validated, so a path that does not exist
  is caught here too rather than being handed to a consumer unchecked. `{path}` therefore always
  refers to something that existed at detection time.
- **A failed detection prints nothing by default.** The error goes through `ffPrintError()`, which
  returns early while `display.showErrors` is `false` — the built-in default. A line that fails is
  simply absent from the output, so the module can look like it was never requested. Use
  `--format json`, which reports `"error"` unconditionally, or set `display.showErrors: true` in the
  config to see the message.
- **`{type}` is never `Unknown`.** It is set to `Visual` or `Editor` before any of the early
  returns, so the `"Unknown"` initialiser in `ffPrintEditor()` is unreachable. It is the only
  field that is always meaningful.
- **`{version}` only exists for a fixed list of editors.** Version detection knows `nvim`, `vim`
  (including `vim.basic`/`vim.tiny`), `nano`, `micro`, `emacs` (including `emacs-29.3`), `hx`,
  `code`, `pluma`, `sublime_text`, `zeditor`, `kak`, `pico` and `ne`. Any other editor prints its
  name alone.
- **Version detection executes the editor.** `nvim`, `vim` and `nano` are first probed by scanning
  the binary for a version literal, but the rest are run — with `--version`, `-version`, or `-h`
  for `ne`. Set `general.detectVersion` to `false` to keep fastfetch side-effect free.
- **The version is a guess from the first line.** After the probe, the output is cut at the first
  newline, then the first run of digits is taken as the version and terminated at the next
  whitespace. An editor whose `--version` prints a banner before the version, or whose version has
  no digits, yields an empty or wrong `{version}`.
- **A snap-installed editor keeps its unresolved path on Linux.** The `realpath()` result is
  discarded when it ends in `/snap`, so `{path}` stays the symlink path for
  snap wrappers while `{exe-name}` is still derived from it.
- **The `$VISUAL` / `$EDITOR` precedence is the standard one, with an empty value treated as unset.**
  The check is on the value's length, so `VISUAL= EDITOR=nano` reports `nano` with `{type}` set to
  `Editor`. Only when both are empty does the module print `$VISUAL or $EDITOR not set`. A value
  that is nothing but whitespace (`VISUAL='   '`) is non-empty and therefore wins the precedence,
  but it splits down to an empty name and is then reported as not an executable — the `$EDITOR` it
  shadowed is never consulted.

## Implementation

`ffDetectEditor()` is a short pipeline:

1. **Pick the variable.** `getenv("VISUAL")` first; if the result has non-zero length, `type` is
   set to `"Visual"`. Otherwise `getenv("EDITOR")` is tried and `type` becomes `"Editor"`. If both
   are empty the function returns the one genuine error string,
   `$VISUAL or $EDITOR not set`.
2. **Take the first word.** The value is truncated at the first whitespace character, so arguments
   such as `-w` or `-f` are dropped and `name` ends up holding the program name alone. Which
   variable the value came from is remembered in a flag, so every failure further down can name it.
3. **Resolve the path.** An absolute value is copied into `path` as-is; anything else goes through
   `ffFindExecutableInPath()`, which searches `$PATH`. Both failure modes — name not found, and the
   empty name a whitespace-only value leaves behind — return the same
   `$VISUAL/$EDITOR does not point to an executable` message.
4. **Canonicalise.** `realpath()` is called on `path` and its failure is reported, since an absolute
   value was copied into `path` before being checked. On Linux the result is only stored when it
   does not end in `/snap`, which keeps snap's wrapper symlink visible instead of the versioned
   binary inside the snap.
5. **Derive the executable name.** Everything after the last `/` (or `\` on Windows) becomes `exe`,
   with a trailing `.exe` stripped on Windows. The two guards around this — no separator in the
   path, empty basename — return a distinct `Failed to determine the executable name`, because a
   `nullptr` here would have been read as success.
6. **Version.** Skipped entirely unless `instance.config.general.detectVersion` is true. `nvim`,
   `vim` and `nano` use `ffBinaryExtractStrings()` with a literal prefix (`NVIM v`,
   `VIM - Vi IMproved `, `GNU nano `); for `vim` the extracted string is additionally cut at the
   first space. Every other known editor is executed with the argument from a fixed table, and the
   first line of its output is reduced to the first numeric token by `strpbrk()` on digits and
   then on whitespace.

The module also `#include`s `detection/libc/libc.h` (`editor.c:4`) and never calls a single symbol
from it. Classifying platform support by includes rather than by symbol usage would wrongly mark
Editor as unsupported on the BSDs, Solaris and Haiku, where the `libc` subsystem has no
implementation.

`ffPrintEditor()` and `ffGenerateEditorJsonResult()` each call `ffDetectEditor()` and each free the
four strbufs through a shared `destroyEditorResult()`. The error path frees them too — it is now a
path that is actually taken, not an unreachable one, and it used to leak all four buffers.
