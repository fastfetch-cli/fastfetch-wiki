# Wallpaper

> Print the file path of the current wallpaper

| | |
|---|---|
| Module type | `wallpaper` |
| Default order | 28 (only used by `--gen-config`) |
| Module source | `src/modules/wallpaper/wallpaper.c` |
| Detection source | `src/detection/wallpaper/` |

Prints the file name of the current desktop wallpaper. The full path is available through the
format string and the JSON output.

```
Wallpaper: image.jpg
Wallpaper: macOS Default Wallpaper
```

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `wallpaper_linux.c` | COSMIC config, otherwise the GTK4 / Qt wallpaper |
| Android | `wallpaper_android.c` + `wallpaper_linux.c` | The `wallpaper` system service over raw binder, or the Linux backends when the display server is not `SurfaceFlinger` |
| FreeBSD / NetBSD / OpenBSD / DragonFly | `wallpaper_linux.c` | Same file |
| Solaris / illumos | `wallpaper_linux.c` | Same file |
| GNU/Hurd | `wallpaper_linux.c` | Same file |
| macOS | `wallpaper_apple.m` | `com.apple.wallpaper` plist on 14+, SQLite / NSWorkspace before that |
| Windows | `wallpaper_windows.c` | `HKCU\Control Panel\Desktop` → `WallPaper` |
| Haiku | `wallpaper_haiku.cpp` | `B_BACKGROUND_INFO` attribute of the Desktop folder |

Android compiles both files and the platform one decides. The binder route reads Android's own
wallpaper and answers while `SurfaceFlinger` is the display server; a Termux X server or a Wayland
session puts a real compositor on top, where the wallpaper belongs to that desktop instead, and the
call is forwarded to the Linux implementation — the same split as `terminalfont_android.c` and
`terminalfont_linux.c`.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | `Wallpaper` | Module key. A single space hides the key and the separator |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

There are no module-specific options.

## Format string

Run `fastfetch -h wallpaper-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{file-name}` | File name — the part after the last path separator |
| `{full-path}` | Full path |

Neither is marked `*` in the help output, so neither is available in the key format. `{file-name}`
is what the default output prints.

```jsonc
{ "type": "wallpaper", "format": "[{file-name}][{full-path}]" }
```

```
Wallpaper: [image.jpg][C:\Users\user\Pictures\image.jpg]
```

## JSON output

```jsonc
[
    {
        "type": "Wallpaper",
        "result": "C:\\Users\\user\\Pictures\\image.jpg"
    }
]
```

- `result` is a **plain string** holding the full path — not an object, and there is no separate
  field for the file name (that is derived by the print function).
- On failure the object carries `error` instead and has no `result`.

## Examples

```jsonc
// Just the file name, which is also the default
{ "type": "wallpaper", "format": "{file-name}" }
```

```jsonc
// Ready to be fed to an image tool
{ "type": "wallpaper", "format": "{full-path}" }
```

## Pitfalls

- **`{full-path}` is not always a path.** On macOS the built-in wallpaper providers have no file
  behind them, and the module substitutes a description — `Built-in aerial photography`,
  `macOS Default Wallpaper`, or `Built-in <provider> wallpaper`. A consumer that opens the value
  has to cope with those.
- **`{file-name}` is split on the platform's own separator only.** The print function looks for
  `/` on Unix and `\` on Windows, so a Windows path written with forward slashes comes back whole
  in `{file-name}`.
- **On Windows an empty `WallPaper` value is a success, not an error.** The registry value exists
  but is empty when the desktop uses a solid colour or a slideshow-managed background, and the
  module then prints an empty line. The same applies to a path that no longer exists: nothing
  verifies it.
- **On Linux only the GTK4 and Qt results are consulted.** `ffDetectGTK4()` is tried first and
  `ffDetectQt()` second; GTK2 and GTK3 wallpapers are ignored. When neither has one, the module
  reports `Failed to detect the current wallpaper path` — which is silent unless
  `display.showErrors` is `true`.
- **COSMIC is a completely separate code path.** With the `cosmic-comp` window manager the module
  reads `~/.config/cosmic/com.system76.CosmicBackground/v1/` directly: `backgrounds` decides
  whether the wallpaper is shared (`all`) or per-output (`output.<name>`), and the chosen file is
  the `Path("…")` value of that entry. Each failure has its own message, from
  `Failed to read COSMIC wallpaper config` to `COSMIC wallpaper path is empty`.
- **The Linux value is unescaped, not validated.** A `file:///` prefix from GTK/Qt is stripped,
  and that is all — no percent-decoding, no existence check.
- **macOS 14+ prefers the plist over the API.** The `com.apple.wallpaper` `Index.plist` is the
  authoritative source on Sonoma and later; `NSWorkspace` is only used when the plist could not be
  read at all. Before 14 the order is reversed: the SQLite `desktoppicture.db` (when built with
  SQLite support) and otherwise `NSWorkspace`, which is described in the source as reliable only
  for user-picked static images.
- **The macOS plist lookup is hard-coded to the first choice.** Only
  `SystemDefault.Desktop.Content.Choices[0]` is read, and within it the first `Files` entry, then a
  serialised `Configuration` blob, then the `Provider` name. A per-space or per-display wallpaper
  can therefore report a different image than the one on screen.
- **On Haiku the wallpaper is per workspace.** The `B_BACKGROUND_INFO` attribute holds a list of
  images with a workspace mask, and the first entry matching the current workspace is used.
- **On Android the reported path can be named but not read.** `/data/system/users/<userId>` is mode
  `0700` and owned by `system`, so an app UID gets `EACCES` from `open`, `stat` and `ls` alike. What
  the module reports is the target of a descriptor the system service opened and handed back,
  resolved with `readlink()` on `/proc/self/fd/` — the directory is never entered. A consumer that
  tries to open the value itself will usually fail, and nothing here verifies that the file still
  exists.
- **On Android the caller has to name itself.** `getWallpaper` takes the caller's package name and
  refuses a name that is not its own with a `SecurityException` — the shorter overload, which passes
  the null name, fails the same way. The name is derived from `/proc/self/exe`; a binary living
  outside an app data directory has none, and only the shell UID can fall back to
  `com.android.shell`. Every other caller gets `Cannot determine the package name of this process`.
- **On Android the value is the platform's own file name, not the image's.** The system wallpaper is
  stored as `wallpaper`, with no extension, so `{file-name}` prints `wallpaper` and the default line
  reads `Wallpaper: wallpaper`. The pre-crop upload sits next to it as `wallpaper_orig` and is never
  reported. The user id is fixed at `0`, so on a device with secondary users the owner's wallpaper
  is what comes back.
- **On Android a live wallpaper has no file at all.** The system wallpaper is asked for first and
  the lock screen only when that first call reports no file — which is exactly how a live wallpaper
  answers. When the lock screen is a live one as well, the module fails with
  `The wallpaper is not a file`, and that failure is silent unless `display.showErrors` is `true`.

## Implementation

`ffDetectWallpaper()` fills a single `FFstrbuf` with the full path and returns an error string or
`nullptr`. `ffPrintWallpaper()` derives the file name from it (the part after the last separator,
or the whole string when there is none) and prints that by default;
`ffGenerateWallpaperJsonResult()` writes the untouched full path to `result`.

### Android

The caller's package name comes from `ffAndroidGetOwnPackage()`, which resolves `/proc/self/exe` and
falls back to `com.android.shell` for the shell UID — nothing else can reach the service. A raw
binder client is opened, the `wallpaper` service is resolved through the service manager, and
`getWallpaper(String callingPackage, int which, int userId)` of `android.app.IWallpaperManager` is
called with the request flag `TF_ACCEPT_FDS`. The transaction code is not written down either: it is
resolved at run time out of the device's own `framework.jar`, by the name of the constant the dex
carries for the method (`TRANSACTION_getWallpaper`), because the numbering is a property of the
`.aidl` the ROM was built from and a vendor that inserts a method ahead of this one shifts it — after
which a transcribed number reaches a different method, or none. A build that does not declare the
method is reported as such instead. The flag is not optional either: a reply carrying
a descriptor is only delivered when the request asked for one, and a request that did not is
answered with `BR_FAILED_REPLY`, which reads like a service that is not running. The descriptor
arrives as a flat binder object and its target is read back with `readlink()` on `/proc/self/fd/`;
the image is never opened, decoded or measured.

`which` 1 is the system wallpaper and is asked for first. `which` 2, the lock screen, is asked only
when the first call reports no file — which is how a live wallpaper answers. `userId` is `0`, the
one user a phone has. An app UID needs no permission of its own to answer here: what the service
checks is that the name being passed belongs to the caller.

### Linux

`ffConnectDisplayServer()` is consulted first for the COSMIC branch. Otherwise
`ffDetectGTK4()->wallpaper` wins over `ffDetectQt()->wallpaper`, and a leading `file://` is
stripped before the value is returned.

Built for Android, this file's entry point is named `ffDetectWallpaperLinux`, and it is
`wallpaper_android.c` that calls it.

### macOS

Three mechanisms, selected by availability:

1. `detectFromPlist()` — reads `~/Library/Application Support/com.apple.wallpaper/Store/Index.plist`
   and walks `SystemDefault.Desktop.Content.Choices[0]`: `Files[0].relative` as a URL, then a
   `Configuration` data blob whose `url.relative` is parsed with `NSPropertyListSerialization`, then
   the `Provider` name with the `com.apple.wallpaper.choice.` prefix removed.
2. `detectFromSQLite()` — a five-way join over `~/Library/Application Support/Dock/desktoppicture.db`
   restricted to `display_id=1 AND space_id=1 AND key=1`, compiled in only when `FF_HAVE_SQLITE3`
   is set.
3. `detectFromNSWorkspace()` — `[NSWorkspace.sharedWorkspace desktopImageURLForScreen:NSScreen.mainScreen]`,
   accepting the result only when it is a file URL.

### Windows

`ffRegOpenKeyForRead(HKEY_CURRENT_USER, L"Control Panel\\Desktop")` followed by
`ffRegReadStrbuf(hKey, L"WallPaper", …)`. Nothing else is queried — the wallpaper *style*
(`WallpaperStyle`, `TileWallpaper`) is not read.

### Haiku

`find_directory(B_DESKTOP_DIRECTORY)` locates the Desktop folder, a `BApplication` is created so
the app_server can be queried, and the `B_BACKGROUND_INFO` attribute is unflattened into a
`BMessage`. The image whose `B_BACKGROUND_WORKSPACES` mask contains the current workspace is used.
