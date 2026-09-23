# Media

> Print the name of the currently playing song

| | |
|---|---|
| Module type | `media` |
| Default order | 48 (only used by `--gen-config`) |
| Module source | `src/modules/media/media.c` |
| Detection source | `src/detection/media/` |

Prints what the system's now-playing interface reports: the artist, the song title, the elapsed and
total time, and the playback status.

```
Media: Daft Punk - Get Lucky - 01:23 / 06:07 (22%) [Playing]
```

The artist is dropped when the same text already appears in the song title (the comparison ignores
case, spaces, `-` and `.`), and both fields are cleaned of the usual YouTube decorations before being
printed. When no media session is found the module reports `No media found` and prints nothing — and
because `display.showErrors` defaults to `false`, the line simply disappears.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `media_linux.c` | MPRIS over the session DBus |
| FreeBSD / OpenBSD / NetBSD / GNU/Hurd | `media_linux.c` | Same code; needs DBus |
| Windows | `media_windows.cpp` | WinRT `GlobalSystemMediaTransportControlsSessionManager` |
| macOS | `media_apple.m` | MediaRemote, or a Perl helper process on macOS 15.4+ |
| Android | none | No detection source is referenced |
| Solaris / illumos | none | No detection source is referenced |
| Haiku | none | No detection source is referenced |

Only the three backends above exist. On a platform without one the module reports
`No media found` rather than "not supported".

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `percent` | object | `{ "green": 100, "yellow": 100, "type": 0 }` | Colour thresholds and style for the percentage. `type: 0` means "use `display.percentType`". |
| `key` | string | `Media` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

The player the data is taken from is **not** a module option: `general.playerName` (the
`--player-name` command-line flag) selects it, and the same setting is used by the `player` module.
On Linux it is an MPRIS bus suffix (`spotify`, `vlc`, …) or a full `org.mpris.MediaPlayer2.…` name;
on Windows it is matched case-insensitively as a substring of the source app
(`AppUserModelId`). With no `playerName`, Linux tries `spotify`, `vlc` and
`plasma-browser-integration` in that order and then walks the bus name list; Windows and macOS ask the
system for the *current* session.

`percent.type` defaults to `0`, which means the module follows `display.percentType`
(`num | num-color` out of the box) — `(22%)`. Setting it to a non-zero bit mask overrides the global
value for this module only; `hide-others` removes the time from the default line, `bar` adds a bar.

## Format string

Run `fastfetch -h media-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{combined}` | The cleaned song title — the title with the decorations removed |
| `{title}` | The raw song title as the backend reported it |
| `{artist}` | The raw artist as the backend reported it |
| `{album}` | The album title |
| `{status}` | `Playing`, `Paused`, `Stopped`, … |
| `{progress}` | Elapsed / total, e.g. `01:23 / 06:07` |
| `{progress-num}` | Percentage, e.g. `22%` — no parentheses |
| `{progress-bar}` | Percentage as a bar |
| `{player-name}` | Friendly player name, e.g. `Google Chrome` |
| `{player-id}` | Bus name (Linux) or bundle / app id (macOS, Windows) |
| `{url}` | Media URL, when the backend provides one |

`{title}` is the untouched backend string while `{combined}` is the cleaned one: only `{combined}`
has `(Official Music Video)`, `[Lyrics]` and the like stripped. `{artist}` is likewise the raw value —
the "already in the title" test and the ` - Topic` / `VEVO` trimming happen only in the default line.
The three `{progress*}` variables are empty when the backend reports no length, and `{progress-num}` /
`{progress-bar}` also depend on `percent.type` (they are empty when the corresponding bit is off).

## JSON output

```jsonc
[
    {
        "type": "Media",
        "result": {
            "song": {
                "name": "Get Lucky",
                "artist": "Daft Punk",
                "album": "Random Access Memories",
                "status": "Playing",
                "length": 367000,
                "position": 83000,
                "cover": null
            },
            "player": {
                "name": "Spotify",
                "id": "spotify",
                "url": ""
            }
        }
    }
]
```

When nothing is playing the module carries an error object instead:

```jsonc
[ { "type": "Media", "error": "No media found" } ]
```

- `length` and `position` are **milliseconds**. `position` is extrapolated from the last update and
  the playback rate while the track is playing, so it advances even if the player only reports its
  timeline on seek.
- `song.name`, `song.artist` and `song.album` are the raw backend strings, i.e. the same values as
  `{title}` / `{artist}` / `{album}`, **not** the cleaned default-line text.
- `cover` is the path of the artwork file, or `null`. See the pitfalls below for when each backend has
  one.
- `player.url` is only filled by the MPRIS backend (the `xesam:url` key); Windows and macOS leave it
  empty. `player.id` is the bus name on Linux, the bundle id on macOS and the `AppUserModelId` on
  Windows.
- The JSON output never applies the percentage configuration or the output format — it is always this
  fixed object.

## Examples

```jsonc
// Title only, no artist, no progress
{ "type": "media", "format": "{title}" }
```

```jsonc
// A custom line with the player and a bar
{ "type": "media", "format": "{player-name}: {artist} - {combined} [{progress-bar}]", "percent": { "type": 2 } }
```

```jsonc
// Hide the elapsed-time text but keep the percentage
{ "type": "media", "percent": { "type": ["num", "num-color", "hide-others"] } }
```

```jsonc
// Only follow a specific player (top-level setting, not a module option)
{ "general": { "playerName": "spotify" }, "modules": [ { "type": "media" } ] }
```

## Pitfalls

- **A missing session is not a crash, it is an error that is hidden by default.** On Windows a machine
  with no media app open reports `winrt: GetCurrentSession() failed`; on Linux without DBus,
  `Fastfetch was compiled without DBus support`. `ffPrintError()` obeys `display.showErrors`, so the
  text run is blank unless `"display": { "showErrors": true }` is set. The JSON run always carries the
  `error` string.
- **`{artist}` and `{title}` are not what the default line shows.** The default line cleans both
  (removes `(Official Music Video)`, `| Lyrics`, …) and drops the artist when it is already part of the
  title, but the format variables are fed the untouched strings. A format that reproduces the default
  line has to do that work itself — or use `{combined}`, which is the cleaned title.
- **The player is chosen globally, not per module.** `playerName` lives under `general`, so it affects
  the `player` module as well, and it cannot be set from the module's own JSON object.
- **Windows needs WinRT and Linux needs DBus at compile time.** A Windows build without WinRT reports
  `Fastfetch is not compiled with WinRT support`; a Linux build without DBus reports
  `Fastfetch was compiled without DBus support`. Neither falls back to another API.
- **macOS 15.4 and newer go through a Perl helper.** `MRMediaRemoteGetNowPlayingInfo()` stopped
  working for third-party processes, so the backend spawns `/usr/bin/perl` — which has to be the
  Apple-signed one, not the Homebrew build — and the dynamic library calls back into fastfetch through
  `DynaLoader`. If Perl is missing or replaced, playback information is unavailable.
- **`cover` is usually `null` in the JSON.** The module asks for the cover with `saveCover = false`, so
  the Windows and macOS backends never write the artwork to a file and the field stays `null`. Only
  the MPRIS backend fills it unconditionally (from `mpris:artUrl`, as a local path), and the Windows /
  macOS paths get a value only when the same run already asked for the cover for a logo.
- **`{url}` is Linux-only.** It comes from the MPRIS `xesam:url` key, so on Windows and macOS the
  variable and the `player.url` field are always empty.
- **A song with no title falls back to the file name.** On Linux, when `xesam:title` is empty but
  `xesam:url` is not, the title is derived from the last path segment of the URL, with percent escapes
  decoded. A session that has neither is treated as "no media" and cleared.

## Implementation

`ffDetectMedia()` fills a single cached `FFMediaResult` through the FFcache layer: the first caller of
a run performs the detection, later callers (the module and the `player` module share it) reuse the
result, and the cache is dropped between `--dynamic-interval` rounds. When the backend returns no song
and no error, `No media found` is filled in, and the song / artist / album / player strings are trimmed
of trailing spaces.

`FFMediaResult` holds `error`, `playerId`, `player`, `song`, `artist`, `album`, `url`, `status`,
`cover`, `length` and `position`. Only the MPRIS backend knows a media URL.

`ffPrintMedia()` builds the default line itself — it cleans `song`, strips ` - Topic` / `VEVO` and the
"artist in title" case from the artist, appends the progress and the percentage, and finally the
status. With a custom `format` the cleaned values are handed to the engine as `{combined}` (cleaned
song) and the raw values as `{title}` / `{artist}`, and the progress, percentage and bar are
pre-rendered into strings so the format engine only has to substitute them.

### Linux / BSD / GNU/Hurd

The session DBus is opened and, without a `playerName`, the well-known names
`org.mpris.MediaPlayer2.spotify`, `.vlc` and `.plasma-browser-integration` are probed in that order;
if none answers, `ListNames` is called on the bus and every `org.mpris.MediaPlayer2.*` name is tried in
turn, skipping `playerctld`. The whole `org.mpris.MediaPlayer2.Player` property set is read in one
`GetAll` call, which yields `Metadata` (the `xesam:` and `mpris:` keys), `PlaybackStatus` and
`Position`. The player's friendly name comes from the `Identity` property, then `DesktopEntry`, then
the bus name; `musikcube` is special-cased because its DBus calls are extremely slow.

### Windows

`GlobalSystemMediaTransportControlsSessionManager` is activated through WinRT and `RequestAsync()` is
polled until the async operation completes. Without a `playerName`, `GetCurrentSession()` is used;
with one, `GetSessions()` is enumerated and the first session whose `SourceAppUserModelId` contains the
name wins (case-insensitively). The title, artist and album come from `TryGetMediaPropertiesAsync()`,
the status from `GetPlaybackInfo()` (`Closed`, `Opened`, `Changing`, `Stopped`, `Playing`, `Paused`),
and the timeline from `GetTimelineProperties()` — the values are in 100-nanosecond units and divided by
10000. While playing, the elapsed time is advanced by the wall-clock time since `LastUpdatedTime`
multiplied by the playback rate. The friendly player name is resolved from the `AppUserModelId`
through `Windows.ApplicationModel.AppInfo` (packaged apps, whose ids contain `!`) and falling back to
the shell's `AppsFolder` namespace; if neither resolves, the id is used with a trailing `.exe` removed.
The cover is read from `TryGetMediaPropertiesAsync().Thumbnail`, written to a temp file with a `fft`
prefix.

### macOS

The `MediaRemote` framework is loaded weakly — `MRMediaRemoteGetNowPlayingInfo()`,
`MRMediaRemoteGetNowPlayingApplicationIsPlaying()`, `...DisplayID()` and `...DisplayName()` — and the
four callbacks are joined with a dispatch group. `kMRMediaRemoteNowPlayingInfoDuration` gives the
length and `kMRMediaRemoteNowPlayingInfoElapsedTime` plus the playback rate and timestamp give the
position. On macOS 15.4 and newer the public API no longer answers for third-party processes, so the
module instead runs `/usr/bin/perl` with a small script that loads fastfetch's own shared object and
installs `ffPrintMediaByMediaRemote` as an XSUB; the helper prints one field per line. The cover is
written to `NSTemporaryDirectory()` as `ff_<uuid>.<ext>` and removed again at exit.
