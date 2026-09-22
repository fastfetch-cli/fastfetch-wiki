# Title

> Print the title, including your username and hostname

| | |
|---|---|
| Module type | `title` |
| Default order | 1 — only used by `--gen-config` |
| Module source | `src/modules/title/title.c` |
| Detection source | — (reads `FFPlatform`, no per-module detection code) |

Prints a single line, `user@host`, on top of the module list:

```
user@my-pc
```

It has no key by default: the initial key is a single space, which hides both the key and its
separator (see [Pitfalls](#pitfalls)). With `display.key.type` left at its default (`string`) and a
key such as `"Title"` the line becomes `Title: user@my-pc`.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `src/common/impl/FFPlatform_unix.c` | Common code, nothing Linux-specific |
| Android | `src/common/impl/FFPlatform_unix.c` | `userShell` is usually empty |
| FreeBSD / NetBSD / OpenBSD / DragonFly / MidnightBSD | `src/common/impl/FFPlatform_unix.c` | |
| Solaris | `src/common/impl/FFPlatform_unix.c` | |
| Haiku | `src/common/impl/FFPlatform_unix.c` | |
| macOS | `src/common/impl/FFPlatform_unix.c` | `fullUserName` comes from the account database |
| Windows | `src/common/impl/FFPlatform_windows.c` | `userShell` is resolved from `SHELL`, then a few fallbacks |

The module itself is platform independent — it only reads the already-populated
`instance.state.platform` (see [Implementation](#implementation)).

## Configuration

| Option | Type | Default | Description |
|---|---|---|---|
| `key` | string | `" "` | Key text. Default is a single space, i.e. the key is hidden. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyIcon` | string | built-in glyph | The icon printed when `display.key.type` includes the icon bit. Set it to any glyph you like, or to `""` to print none. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `outputColor` | color | – | Overrides `display.color.output`. Only affects `format`; the default `user@host` uses `color` below. |
| `format` | string | – | Output format string. See [Format string](#format-string). |
| `fqdn` | boolean | `false` | Print the fully qualified domain name instead of truncating at the first `.`. |
| `color.user` | color | – | Color of the user name. Falls back to `display.color.title`. |
| `color.at` | color | – | Color of the `@`. |
| `color.host` | color | – | Color of the host name. |
| `condition` | object | – | See [Configuration](Modules/Global#condition-in-practice). |

Notes:

- `color.*` only apply to the **default** `user@host` layout. If you set `format`, use the
  `{user-name-colored}`, `{at-symbol-colored}` and `{host-name-colored}` variables to get the same
  effect — they are the only pre-colored variables in fastfetch.
- With `fqdn: false` the host name is cut at the first `.`, so `my-pc.local` prints as
  `my-pc`. `hostName` in the JSON output is *not* truncated.
- The icon is purely cosmetic and exists only to look good in a Nerd Font; replace it or set
  `"keyIcon": ""` to drop it.
- Module options are **JSON-config only**. Since 2.52.0 there are no `--title-*` command line
  flags (see [Global](Modules/Global#pitfalls-that-bite-across-modules)).

```jsonc
{
  "modules": [
    {
      "type": "title",
      "key": "Title",
      "fqdn": true,
      "color": { "user": "blue", "at": "white", "host": "green" }
    }
  ]
}
```

## Format string

Run `fastfetch -h title-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{user-name}` | User name |
| `{host-name}` | Host name (respects `fqdn`) |
| `{home-dir}` | Home directory, as reported by the OS (usually with a trailing slash) |
| `{exe-path}` | Absolute path of the running `fastfetch` binary |
| `{user-shell}` | The user's login shell |
| `{user-name-colored}` | User name, wrapped in `color.user` |
| `{at-symbol-colored}` | `@`, wrapped in `color.at` |
| `{host-name-colored}` | Host name (respects `fqdn`), wrapped in `color.host` |
| `{full-user-name}` | Full / display name from the account database |
| `{user-id}` | UID on Unix, SID on Windows (see [Pitfalls](#pitfalls)) |
| `{pid}` | PID of the running `fastfetch` process |
| `{cwd}` | Current working directory with the home directory replaced by `~` |

## JSON output

```jsonc
{
  "type": "Title",
  "result": {
    "userId": 1000,
    "userName": "user",
    "fullUserName": "Jane Doe",
    "hostName": "my-pc.local",
    "homeDir": "/home/user/",
    "exePath": "/usr/local/bin/fastfetch",
    "userShell": "/bin/bash",
    "pid": 1234,
    "cwd": "/home/user/projects/"
  }
}
```

`userId` is a **number** on Unix and a **string** on Windows, where it carries the SID.

## Examples

Hide the key completely and print a fully qualified host name:

```jsonc
{ "modules": [ { "type": "title", "key": " ", "fqdn": true } ] }
```

Colored user and host names without touching `color.*`:

```jsonc
{
  "modules": [
    { "type": "title", "format": "{user-name-colored} at {host-name-colored}" }
  ]
}
```

Print the title with a user-provided format string:

```jsonc
{ "modules": [ { "type": "title", "format": "{user-name} in {cwd} on {host-name}" } ] }
```

```
user in /home/user/projects on my-pc
```

## Pitfalls

- **The default key is a single space, not empty.** `key: " "` hides the key *and* the separator.
  Setting `key: ""` does the opposite of hiding: it falls back to the localized module name
  (`Title: …`). This is the same convention as every other module — see
  [Global](Modules/Global#options-every-module-accepts).
- **`{user-id}` changes type across platforms.** On Unix it is a UID (number); on Windows it is a
  SID string. Anything that parses the JSON output must accept both.
- **A missing `fqdn` does not affect `{host-name}`'s JSON counterpart.** `result.hostName` is always
  the full name.
- **`{cwd}` collapsing is prefix-based and case-sensitive.** It compares `cwd` against `homeDir`,
  which the OS reports *with* a trailing slash. A `cwd` that equals the home directory without that
  slash is printed in full instead of `~`. On Windows the comparison is case-insensitive and the
  result uses a forward slash (`~/sub`).
- **`{cwd}` is trimmed of trailing slashes** (except for the root `/`), so it does not always read
  back as `result.cwd` from the JSON output.
- **The Title module has a side effect.** It stores `fqdn` into `instance.state.titleFqdn`, which
  the [Separator](Modules/Separator) module reads to size its automatic rule. If you print a separator
  without a title, the separator sizes itself from a non-FQDN host name.
- **`fqdn: true` on Windows** prints the full computer name (e.g. `PC.domain.local`), which is
  usually not what users expect from a "hostname".

## Implementation

`ffPrintTitle()` (`src/modules/title/title.c`) has two branches:

1. **Default layout.** Builds three string buffers (`userNameColored`, `atColored`,
   `hostNameColored`) with the private `appendText()` helper, then
   `ffPrintLogoAndKey()` + `ffStrbufWriteTo()` for each part.

   `appendText()` emits `FASTFETCH_TEXT_MODIFIER_BOLT` when `display.brightColor` is on, then
   `\e[<color>m`, then the text, then a reset. Everything ANSI-related is skipped when
   `display.pipe` is set (`--pipe`), so the pipe output is plain text.

   The `@` gets special treatment: it is only colored when `color.at` is non-empty, and unlike the
   other two parts it does **not** fall back to `display.color.title`.

2. **`format` branch.** Computes a tilde-collapsed `cwd` first, then calls
   `FF_PRINT_FORMAT_CHECKED` with the 12 format arguments. The tilde collapse is a
   `ffStrbufStartsWith` (case-insensitive on Windows) against `platform.homeDir`; the leading part
   is replaced with `~/` and trailing slashes are trimmed while `length > 1`.

`ffGenerateTitleJsonResult()` writes nine fields straight out of `instance.state.platform` and
hardcodes the `sid`/`uid` split with `#ifdef _WIN32`.

`instance.state.titleFqdn` is assigned on every call, before any output, so the Separator module
sees the current value even in `--structure title:separator`.
