# TerminalFont

> Print the font name and size used by the current terminal

| | |
|---|---|
| Module type | `terminalfont` |
| Default order | 30 (only used by `--gen-config`) |
| Module source | `src/modules/terminalfont/terminalfont.c` |
| Detection source | `src/detection/terminalfont/` |

Prints the font the terminal is configured to use, with a fallback font appended when the terminal
has one.

```
Terminal Font: JetBrainsMono Nerd Font (12pt)
Terminal Font: Cascadia Mono (12pt)
Terminal Font: Menlo (11.25pt, Medium)
Terminal Font: JetBrainsMono Nerd Font (12pt) / Noto Sans Mono
```

The parenthesised part is built from the size and the styles: the size gets a `pt` suffix unless it
already ends in `pt` or `px`, and styles are appended after a comma. A font with no name at all is
printed as `default (…)`.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `terminalfont_linux.c` | GSettings / DConf, INI and TOML config files, `.Xresources`, `/etc/vconsole.conf` |
| Android | `terminalfont_android.c` + `terminalfont_linux.c` | Termux first, then the Linux backends |
| FreeBSD / NetBSD / OpenBSD / DragonFly | `terminalfont_linux.c` | Same file |
| Solaris / illumos | `terminalfont_linux.c` | Same file |
| Haiku | `terminalfont_linux.c` | Same file; adds a Haiku Terminal branch |
| GNU/Hurd | `terminalfont_linux.c` | Same file |
| macOS | `terminalfont_apple.m` | iTerm2 and Warp read their plists; Apple Terminal is asked over AppleScript |
| Windows | `terminalfont_windows.c` | Windows Terminal `settings.json` + fragments, ConHost, ConEmu, mintty, Warp |

Every platform has an implementation. Which terminals a given platform can answer for is a much
shorter list — see the pitfalls.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | `Terminal Font` | Module key. A single space hides the key and the separator |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

There are no module-specific options. One environment variable is read by the Windows Terminal
backend:

| Variable | Effect |
|---|---|
| `WT_PROFILE_ID` | Selects which Windows Terminal profile the font is resolved from. Without it, only `profiles.defaults` applies |

## Format string

Run `fastfetch -h terminalfont-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{combined}` | The primary font's pretty string — name, size and styles together |
| `{name}` | Font family name |
| `{size}` | Font size, as the backend reported it |
| `{styles}` | Font styles, joined with `, ` |

None of the four is marked `*` in the help output, so none is available in the key format.

All four describe the **primary** font; the fallback font is not reachable from a format string at
all. `{size}` is the raw value — the `pt` suffix that `{combined}` adds is not part of it, so a
Windows Terminal size of `12` prints as `{size}` = `12` and inside `{combined}` as `12pt`.

## JSON output

```jsonc
[
    {
        "type": "TerminalFont",
        "result": {
            "font": {
                "name": "Cascadia Mono",
                "size": "12",
                "styles": [],
                "pretty": "Cascadia Mono (12pt)"
            },
            "fallback": {
                "name": "",
                "size": "",
                "styles": [],
                "pretty": ""
            }
        }
    }
]
```

- `result` holds two objects, `font` and `fallback`, each with the keys `name`, `size`, `styles`,
  `pretty` in that order.
- `styles` is an **array** of strings, empty when the font has no style. It is the only list-valued
  field in the module.
- On failure the object carries `error` (a string) instead and has no `result`.
- An empty fallback is four empty values, not an omitted object.

## Examples

```jsonc
// Family only, without the derived pretty string
{ "type": "terminalfont", "format": "{name}" }
```

```jsonc
// Size and styles separately
{ "type": "terminalfont", "format": "{name} {size} [{styles}]" }
```

## Pitfalls

- **`{combined}` is not "primary plus fallback".** Despite the name it is exactly the primary
  font's `pretty` string. The fallback only ever appears in the default text output, separated by
  ` / `, and in the JSON's `fallback` object.
- **Almost every backend substitutes a hard-coded default when its config file is missing.** The
  values are `Consolas` / `monospace` / `Menlo` for Alacritty, `Cascadia Mono` 12 for Windows
  Terminal, `Cascadia Code` 18 for Rio, `JetBrainsMono Nerd Font` 12 (13 on macOS) for Ghostty,
  `Lucida Console` 9 for mintty, `Hack` 13 for Warp, `monospace` 11.0 for kitty, `Mono` 10 for
  Terminator, `Noto Sans Mono` 11 for Deepin Terminal and `Noto Sans Mono` 14px for COSMIC Term.
  A printed font therefore does not prove the terminal is configured that way.
- **An error discards the whole result.** `ffDetectTerminalFont()` reports failure whenever
  `error` is non-empty, so a backend that parses the first font successfully and then complains
  about a second one (urxvt with a font format it does not understand) yields an `error` field and
  no `result` at all.
- **Only a fixed set of terminals is supported.** Everything else — `xterm`-likes without a config,
  `dumb`, `tmux`, `screen`, `sshd`, `zellij`, `zed`, `Tabby` on Linux, `Windows Explorer` — ends
  in `Unknown terminal: <name>`, which is silent unless `display.showErrors` is `true`.
- **A PTS terminal is explicitly unsupported on Unix.** When the terminal's `exe` starts with
  `/dev/pts/`, the module reports `Terminal font detection is not supported on PTS` instead of
  guessing. A `/dev/tty*` exe instead goes through the Linux virtual-console path, which reads
  `/etc/vconsole.conf` and falls back to running `showconsolefont --info`.
- **Font detection needs terminal detection.** The first thing the module does is call
  `ffDetectTerminal()`; when that produced no process name the error is
  `Terminal font needs successful terminal detection`, and every `Modules-Terminal` pitfall
  applies unchanged.
- **Several backends execute other programs.** `wezterm-gui ls-fonts --text a`,
  `contour font-locator`, `kitty +kitten query-terminal`, `showconsolefont --info`, and on macOS
  `osascript` for Apple Terminal. Unlike version probing, font detection is **not** gated on
  `general.detectVersion`.
- **Ghostty's config is followed through `config-file` includes.** Up to 16 included files are
  loaded, each unique path only once (which also breaks cycles), relative paths are resolved
  against the file containing the directive, and a `?` prefix is stripped. Files referenced by
  `config-file` take effect after all root config files, in the order they were found.
- **Windows Terminal resolves the font through an inheritance chain.** A value from the active
  profile wins over `profiles.defaults`, which wins over a JSON fragment's `updates` entry, which
  wins over a fragment's `guid` entry; unset means the built-in `Cascadia Mono` 12. Fragments are
  read from `<LocalAppData>\Microsoft\Windows Terminal\Fragments\<app>\*.json` and
  `<ProgramData>\…` (user scope first, since Windows Terminal merges it first), are never recursed
  into, and are only consulted when `settings.json` did not fully specify the font. A portable
  install (`<exe dir>\.portable`) reads `settings\settings.json` instead; a `cmd.exe` one-liner is
  the last resort when the path cannot be determined.
- **On Android without FreeType the module degrades instead of failing.** Termux reports
  `monospace` plus the size from its SharedPreferences and the error string
  `Fastfetch was built without freetype2 support`; with FreeType the family name is read out of
  `~/.termux/font.ttf`. Either way an error string is present, so the JSON path shows `error`
  rather than a `result`.
- **Styles are parsed differently per backend, and the shape differs.** Qt, Pango, XLFD, Xft and
  Windows values all end up in the same `styles` list, but a Pango style is split on spaces, an Xft
  `weight` / `slant` / `width` has its dashes removed (`Semi-Bold` → `SemiBold`), and an XLFD
  `normal` field is dropped. `{styles}` therefore is not a stable enum.
- **`{size}` may carry a unit that the backend chose.** Xft adds `pt` or `px` depending on the key
  it came from, XLFD converts decipoints (`%ld.%ldpt`), and Warp appends `px`. `{combined}` then
  does not add a second suffix — but it does add `pt` to a bare number.

## Implementation

`ffDetectTerminalFont()` fills an `FFTerminalFontResult` — two `FFfont` values (`pretty`, `name`,
`size`, `styles`) plus an `error` strbuf — and dispatches in three stages:

1. `ffDetectTerminal()` must produce a process name.
2. `detectTerminalFontCommon()` handles the terminals that are the same everywhere: `alacritty`,
   `wezterm-gui`, `tabby`, `contour`, `ghostty`, `Muxy`, `rio`, and — outside Windows — `kitty`,
   `/dev/pts/*` and `/dev/tty*`.
3. `ffDetectTerminalFontPlatform()` (one implementation per platform) handles the rest.

If neither stage matched, the result is `Unknown terminal: <processName>`.

`ffPrintTerminalFont()` prints `font.pretty`, then `" / "` plus `fallback.pretty` when the fallback
has one. `ffGenerateTerminalFontJsonResult()` writes both objects; the `error` field is written with
`yyjson_mut_obj_add_strbuf()` because the message lives in the result's own strbuf.

`fontInitPretty()` (`src/common/impl/font.c`) builds the pretty string:

```c
name + " (" + size [+ "pt"] [+ ", " + styles…] + ")"
```

with `default` substituted for an empty name when a size or style exists.

### Unix

`terminalfont_linux.c` is a long dispatch table over the process name. The mechanisms it uses:

- **GSettings / DConf** — `gnome-terminal`, `tilix`, `mate-terminal`, `ptyxis-agent` and `kgx`
  resolve their default profile through the profile list, then read `font` unless
  `use-system-font` is set, in which case the desktop's `monospace-font-name` is used
  (Cinnamon and MATE have their own key paths).
- **INI-like config files** — konsole / yakuake (`konsolerc` / `yakuakerc` → profile file),
  `lxterminal`, `sakura`, `termite`, `foot`, `qterminal`, `xterm` and `urxvt`
  (`.Xresources` / `.Xdefaults`), `weston-terminal`, `kmscon`, `terminator`.
- **XFConf** — `xfce4-terminal` reads `xfce4/terminal/terminalrc` or the newer
  `xfconf/xfce-perchannel-xml` file, then `xsettings::/Gtk/MonospaceFontName`.
- **Reading the binary or the process** — `st` looks for `-f` in `/proc/<pid>/cmdline` and
  otherwise scans the executable for a `size=` literal.
- **Plain file reads** — COSMIC Term reads two files under
  `~/.config/cosmic/com.system76.CosmicTerm/v1/`.

### macOS

`iterm.app` / `iTermServer-*` reads `com.googlecode.iterm2.plist` and picks the bookmark whose
`Name` equals `$ITERM_PROFILE`, using `Normal Font` and, when `Use Non-ASCII Font` is on,
`Non Ascii Font` as the fallback. `Apple_Terminal` is queried with
`osascript -e 'tell application "Terminal" to font name of window frontmost & " " & font size of window frontmost'`.
`WarpTerminal` reads `dev.warp.Warp-Stable.plist`.

### Windows

`Windows Terminal` / `WindowsTerminal.exe` go through `detectFromWindowsTerminal()` (settings.json,
portable mode, MS Store paths, then the `cmd.exe` fallback), `mintty` reads `mintty/config` or
`.minttyrc`, `conhost.exe` calls `GetCurrentConsoleFontEx()`, `ConEmu*` parses `ConEmu.xml` from
`$ConEmuDir` / `$ConEmuBaseDir` / `$APPDATA`, and `warp*` reads
`HKCU\Software\Warp.dev\Warp` (`FontName`, `FontSize`, `FontWeight` — the last one becomes the
single style entry).
