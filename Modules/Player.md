# Player

> Print the music player name that is currently active

| | |
|---|---|
| Module type | `player` |
| Default order | 47 (only used by `--gen-config`) |
| Module source | `src/modules/player/player.c` |
| Detection source | `src/detection/media/` (shared with [Media](Modules/Media)) |

An alias of the [Media](Modules/Media) module that prints only the player instead of the song. It
reads the same shared `FFMediaResult` and prints a single line:

```
Media Player: Youtube (Google Chrome)
```

The value is a "pretty" name built from two fields: when the media has a URL, the site's name is
derived from it and the player is appended in parentheses. Without a URL the line is just the player
name, e.g. `Spotify`.

## Platform support

Identical to [Media](Modules/Media): `player` has no detection source of its own and calls
`ffDetectMedia()`.

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `media_linux.c` | MPRIS over the session DBus |
| FreeBSD / OpenBSD / NetBSD / GNU/Hurd | `media_linux.c` | Same code; needs DBus |
| Windows | `media_windows.cpp` | WinRT `GlobalSystemMediaTransportControlsSessionManager` |
| macOS | `media_apple.m` | MediaRemote, or a Perl helper process on macOS 15.4+ |
| Android | none | No detection source is referenced |
| Solaris / illumos | none | No detection source is referenced |
| Haiku | none | No detection source is referenced |

On a platform without a backend the module reports the backend's error (for example
`winrt: GetCurrentSession() failed` on Windows with no media app open), not a "not supported" message.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | `Media Player` | Module key. A single space hides the key and the separator |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

There is **no module-specific option** — not even `percent`, which `media` has. Every key other than
the generic ones produces `Unknown JSON key …`.

The player that is reported is not chosen here either: `general.playerName` (the `--player-name` flag)
selects it, and the same setting drives `media`. It cannot be set from the module's own object.

## Format string

Run `fastfetch -h player-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{player}` | Pretty player name — the value the default line prints |
| `{name}` | Player name, exactly as the backend reported it |
| `{id}` | Player identifier: the MPRIS bus name on Linux, the bundle id on macOS, the `AppUserModelId` on Windows |
| `{url}` | Media URL, when the backend provides one |

None of the four is marked `*` in the help output, so none is available in the key format.

The difference between `{player}` and `{name}` is only the website prefix: `{name}` is the raw backend
string (`Google Chrome`) while `{player}` is the composed one (`Youtube (Google Chrome)`). When there
is no URL — or the URL does not start with `http://` / `https://` — the two are identical.

## JSON output

```jsonc
[
    {
        "type": "Player",
        "error": "Player module is an alias of Media module"
    }
]
```

**This is the only output the module ever produces in JSON**, whether or not a player is detected:
`ffGeneratePlayerJsonResult()` writes that error and returns `false` without calling the detection
layer at all. There is no `result` object, and `--show-errors` changes nothing because the error is
not routed through `ffPrintError()`.

To get the player in machine-readable form, read the `player` object of the [Media](Modules/Media)
module instead — `player.name`, `player.id` and `player.url` correspond to `{name}`, `{id}` and
`{url}`.

## Examples

```jsonc
// The raw player name instead of the pretty one
{ "type": "player", "format": "{name}" }
```

```jsonc
// Site and identifier
{ "type": "player", "format": "{player} ({id})" }
```

```jsonc
// Follow one player (top-level setting, not a module option)
{ "general": { "playerName": "spotify" }, "modules": [ { "type": "player" } ] }
```

```jsonc
// Rename the line, since the default key is two words
{ "type": "player", "key": "Now playing in" }
```

## Pitfalls

- **The JSON output is always an error.** `{"type": "player"}` in a `--format json` run yields
  `Player module is an alias of Media module` and nothing else. Use `media` if the value is needed by
  a script.
- **The default key is `Media Player`, not `Player`.** The module's type name (`player`) and its
  display name differ, so `keyWidth` and `display.key.width` align on the two-word name, and a config
  that filters on the key text has to match `Media Player`.
- **The website prefix is skipped only for `spotify` and `vlc`, and the test is on the id, not the
  name.** `ffStrbufIgnCaseEqualS(&media->playerId, "spotify")` compares the *player identifier*, which
  is the bare MPRIS bus name on Linux but a full `AppUserModelId` on Windows and a bundle id on
  macOS. The exemption therefore only really applies on Linux: a Windows Spotify reports an id like
  `SpotifyAB.SpotifyMusic_…!Spotify`, which is not equal to `spotify`.
- **The site name is derived by crude string surgery.** Everything up to and including `https://`,
  `http://`, `https://www.` or `http://www.` is dropped, then the rest is cut at the first `/` and at
  the last `.`. So `https://music.youtube.com/watch?…` becomes `music.youtube` — the subdomain is
  kept, and the first letter is only capitalised when no `.` remains at all. Other schemes
  (`file://`, `spotify:track:…`) leave `{player}` equal to `{name}`.
- **A player that reports no name is an error, not an empty line.** The module distinguishes two
  failures: the backend's own error (`No media found`, `winrt: GetCurrentSession() failed`, …) is
  printed first, and a successful detection whose `player` string is empty yields
  `No media player detected`.
- **Errors are invisible by default.** Both messages go through `ffPrintError()`, which obeys
  `display.showErrors`; with its default `false` the module simply prints nothing.
- **`{url}` is Linux-only.** It comes from the MPRIS `xesam:url` key, so on Windows and macOS it is
  always empty — and therefore `{player}` and `{name}` are always the same there.

## Implementation

`ffPrintPlayer()` calls `ffDetectMedia(false)` — the same cached result the `media` module uses, so
the two never probe the system twice in one run, and `saveCover = false` means no artwork is written.

The pretty name is built in the module, not in the detection layer:

1. If `url` is non-empty and the player id is neither `spotify` nor `vlc` (case-insensitively), the
   scheme and an optional leading `www.` are skipped by adding a fixed offset into the string
   (12, 11, 8 or 7 characters).
2. The remainder is cut before the first `/` (dropping the path) and before the last `.` (dropping the
   TLD).
3. If something is left over and it contains no `.`, the first byte is upper-cased with `toupper`.
4. The player name is appended, wrapped in ` ( … )` when step 2 left a site name.

Step 2 can empty the string — that happens when the host has nothing before its last dot, as in
`https://./` — and then the module falls back to the bare player name. A dotless host such as
`https://localhost/` survives both cuts and is simply capitalised to `Localhost`.
