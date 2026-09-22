# Icons

> Print icon style name

| | |
|---|---|
| Module type | `icons` |
| Default order | 25 (only used by `--gen-config`) |
| Module source | `src/modules/icons/icons.c` |
| Detection source | `src/detection/icons/` |

The module answers two different questions depending on the platform: on Linux and the BSDs it names
the **icon theme** the desktop is configured with, and on Windows it lists the **desktop icons that
are currently shown**. Both are reported as two pre-grouped strings, printed as one line separated by
`, `:

```
Icons: This PC, Recycle Bin
```

The first part is only printed when it is non-empty, so a Windows machine that shows none of the
first group's icons prints just the second part — and a machine that shows nothing at all prints
nothing (see Pitfalls).

The split between the two strings is fixed by the backend, not by the module: on Windows the first
string carries *This PC* and *User's Files*, the second one *Remote Network*, *Recycle Bin* and
*Control Panel*; on Linux the first string is the Qt/Plasma theme and the second the merged GTK
themes. There is no option that changes the grouping.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `icons_linux.c` | Qt/Plasma and GTK2/3/4 settings |
| Android | `icons_linux.c` | Same code; needs a display server |
| FreeBSD | `icons_linux.c` | Same code |
| NetBSD | `icons_linux.c` | Same code |
| OpenBSD | `icons_linux.c` | Same code |
| Solaris / illumos | `icons_linux.c` | Same code |
| Haiku | `icons_nosupport.c` | Not supported |
| GNU/Hurd | `icons_linux.c` | Same code as Linux |
| macOS | `icons_nosupport.c` | Not supported |
| Windows | `icons_windows.c` | Registry, `HideDesktopIcons` |

macOS and Haiku are the two platforms where the module is a stub: both keep the module registered and
report the standard `Not supported on this platform` message, which is silent unless
`display.showErrors` is on.

## Configuration

The module defines no keys of its own: `option.h` contains nothing but the shared `FFModuleArgs`.

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

## Format string

Run `fastfetch -h icons-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{icons1}` | First group (Qt theme on Linux, *This PC* / *User's Files* on Windows) |
| `{icons2}` | Second group (merged GTK themes on Linux, the remaining desktop icons on Windows) |

Neither variable exists in the key format: `ffPrintLogoAndKey()` parses `key` with `{index}`,
`{icon}` and `{module-name}` only, so `"key": "{icons1}"` prints a literal `{icons1}`. Two further
details of the shared format engine matter here:

- **An unknown placeholder is copied verbatim, conditional syntax included.** A key of
  `"[{?index}I{index}{?}][{?icons1}A{icons1}{?}]"` prints `[][{?icons1}A{icons1}{?}]`.
- **A conditional treats an empty string as unset.** `formatArgSet()` counts a string argument as set
  only when its length is non-zero, so `{?icons1}…{?}` is the right way to suppress the separator
  when the first group is empty on Windows.

## JSON output

```jsonc
[
    {
        "type": "Icons",
        "result": {
            "icons1": "This PC",
            "icons2": "Recycle Bin"
        }
    }
]
```

Both fields are always present; an empty group is `""`. The two strings are exactly the ones the
default line joins, so no re-splitting is needed. A detector error — including the Linux
"nothing found" and Windows "everything hidden" cases — produces
`{ "type": "Icons", "error": "…" }` with no `result`.

## Examples

```jsonc
// The second group alone, which on Windows is the icon set most people care about
{ "type": "icons", "format": "{icons2}" }
```

```jsonc
// Mark the two groups instead of relying on the "', '" the default line inserts
{ "type": "icons", "format": "{?icons1}{icons1}{?}{?icons2} + {icons2}{?}" }
```

## Pitfalls

- **On Windows the module reports the opposite of what the registry stores.** The five values under
  `HideDesktopIcons` are *hide* flags (a non-zero value means the icon is hidden), and the module
  lists the icons whose flag is `0`, i.e. the ones currently **shown**. Reading the registry by hand
  to compare gives inverted results.
- **On Windows the built-in defaults are the "fresh install" state, not "nothing shown".** When the
  key does not exist the module assumes Windows' own defaults — *This PC*, *User's Files*,
  *Remote Network* and *Control Panel* hidden, *Recycle Bin* shown — and answers `Recycle Bin`
  without reading a single value. A machine whose desktop has been customised before the key was
  created is not distinguishable from a fresh one.
- **On Windows, when all five icons are hidden the module reports an error instead of an empty
  line.** The message is `All icons are hidden` (silent unless `display.showErrors` is on), and the
  JSON path returns an `error` field rather than `""`/`""`. Note that this includes the Recycle Bin,
  whose default is "shown": hiding *only* the Recycle Bin leaves the other four defaults in place, so
  the "all hidden" branch is reached exactly when every one of the five is hidden.
- **On Windows the module is about desktop icons, not an icon theme.** The registry description
  ("Print icon style name") describes the Linux behaviour; on Windows there is no theme to read, and
  the module answers a different question with the same two-string shape.
- **On Linux a TTY session is an error, not an empty result.** The module reports
  `Icons aren't supported in TTY` and prints nothing, rather than falling back to the GTK settings
  that may well be configured.
- **On Linux the Qt and GTK themes are read from different places and can disagree.** `{icons1}`
  holds only the Qt/Plasma theme (with a ` [Qt]` label appended), while `{icons2}` is the GTK2/GTK3/
  GTK4 merge produced by the shared `ffParseGTK()` helper. When all three GTK themes are equal the
  label is ` [GTK2/3/4]`, and combinations such as ` [GTK2/3], … [GTK4]` are possible — so
  `{icons2}` is a labelled fragment, not a bare theme name. Nothing reconciles the two groups.
- **On Linux nothing is printed when the GTK and Qt probes all come up empty**, unless
  `display.showErrors` is on, where the message is `No icons could be found`.
- **The GTK/Qt probes are cached, and re-read on every `--dynamic-interval` round.** They live in the
  `FFcache` layer, whose entries are dropped at each round boundary, so switching the icon theme is
  visible on the next round without restarting fastfetch.
- **`ffPrintError()` writes to stdout.** With `display.showErrors: true` an error line is printed
  before the JSON array, so the run is no longer parseable. Keep `showErrors` off in anything that
  pipes the JSON output.

## Implementation

`ffPrintIcons()` and `ffGenerateIconsJsonResult()` each call `ffDetectIcons()` on a two-string
result and then do nothing but serialise it — the join in the print path is
`{icons1}` + `", "` (only when `{icons1}` is non-empty) + `{icons2}`. The detection layer is not
cached, but the GTK/Qt layer it depends on is.

### Windows

`HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\HideDesktopIcons\NewStartPanel` is tried
first, then `…\ClassicStartMenu`. Five named values are read into variables that start out at
Windows' defaults — `ThisPC = 1`, `UsersFiles = 1`, `RemoteNetwork = 1`, `RecycleBin = 0`,
`ControlPanel = 1` — and an unreadable value keeps its default. The value names are the well-known
shell GUIDs (written here in lower case):

| Value name | Icon |
|---|---|
| `{20d04fe0-3aea-1069-a2d8-08002b30309d}` | This PC |
| `{59031a47-3f72-44a7-89c5-5595fe6b30ee}` | User's Files |
| `{f02c1a0d-be21-4350-88b0-7367fc96ef3c}` | Remote Network |
| `{645ff040-5081-101b-9f08-00aa002f954e}` | Recycle Bin |
| `{5399e694-6ce5-4d6c-8fce-1d8870fdcba0}` | Control Panel |

`icons1` is assembled from the two `0` flags of the first group, `icons2` from the three of the
second, each with a trailing `, ` that is trimmed afterwards.

### Linux, Android, FreeBSD, NetBSD, OpenBSD, Solaris, GNU/Hurd

The display-server result is consulted first: a TTY session aborts. Otherwise the Qt/Plasma icon
theme goes into `icons1` with a ` [Qt]` suffix, and `icons2` is built by the shared `ffParseGTK()`
helper from the GTK2, GTK3 and GTK4 icon themes. When all four sources are empty the module reports
`No icons could be found`.
