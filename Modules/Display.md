# Display

> Print resolutions, refresh rates, etc

| | |
|---|---|
| Module type | `display` |
| Default order | 17 (only used by `--gen-config`) |
| Module source | `src/modules/display/display.c` |
| Detection source | `src/detection/displayserver/` |

Prints one line per connected display: the configured resolution, the HiDPI scale factor, the
physical diagonal, the refresh rate, and markers for the display type and the primary display.

```
Display (Color LCD): 3456x2234 @ 2x in 16", 120 Hz [Built-in] *
Display (Sample Monitor): 3840x2160 @ 2x in 32", 60 Hz [External]
```

Every part after the resolution is conditional: `@ <n>x` appears only when the DPI is not 96,
`in <n>"` only when the physical size is known and the diagonal is longer than an inch, and the
refresh rate only when one was reported. `[Built-in]`/`[External]` is omitted when the type is
unknown, and the trailing `*` marks the primary display — but only when more than one display is
connected.

`Display` and [Monitor](Modules/Monitor) are the same detection behind two different default
layouts. `Brightness`, `DE`, `WM`, `WMTheme`, `Theme`, `Icons`, `Font`, `Cursor`, `Wallpaper` and
`TerminalFont` read the same detection result — see [Implementation](#implementation).

## Platform support

The module layer is platform independent. The detection layer is the `displayserver` subsystem,
which is shared by several modules and always has a real implementation on all ten platforms.

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `linux/displayserver_linux.c` + `common.c`, `wmde.c`, `xcb.c`, `xlib.c`, `drm.c`, `wayland/` | Wayland, then XCB, then Xlib, then DRM/sysfs |
| Android | `displayserver_android.c` + `common.c`, `wmde.c`, `xcb.c`, `xlib.c`, `wayland/` | `cmd`, then `dumpsys`, then `getprop`. **No `drm.c`** |
| FreeBSD | `linux/displayserver_linux.c` + the same helper set as Linux | Plus a `kenv` fallback for text consoles |
| NetBSD | `linux/displayserver_linux.c` + the same helper set as Linux | |
| OpenBSD | `linux/displayserver_linux.c` + the same helper set as Linux | |
| Solaris/illumos | `linux/displayserver_linux.c` + the same helper set as Linux | |
| Haiku | `displayserver_haiku.cpp` | `BScreen` |
| GNU/Hurd | `linux/displayserver_linux.c` + a **reduced** Wayland set | Only `wayland/wayland.c`; the KDE, xdg-output and colour-management protocol files are not compiled |
| macOS | `displayserver_apple.c` | CoreGraphics |
| Windows | `displayserver_windows.c` | GDI / `DisplayConfigGetDeviceInfo` |

`src/detection/displayserver/displayserver.c` itself is in the common source list
(`CMakeLists.txt:483`) and holds the cache entry, `ffdsAppendDisplay()` and the DPI normalisation.

Because the Linux implementation is reused on the BSDs and Solaris, those platforms inherit the
same backend order — Wayland first, then X11 through XCB, then X11 through Xlib, and only if all
of them fail, DRM. On a machine with no display server at all (a text console, a container), the
DRM/sysfs path is what answers.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | module name + display name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works, `""` prints none. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). Ignored by the compact layouts. |
| `condition` | object | – | Show the module only if the conditions match. |
| `compactType` | string or `null` | `"none"` | Lay all displays out on a single line (see below). |
| `preciseRefreshRate` | boolean | `false` | Print the refresh rate as reported instead of rounding it to a whole number. |
| `order` | string or `null` | `"none"` | Sort the displays by name: `"asc"`, `"desc"` or `"none"`. |

The default `key` is `Display (<name>)`, falling back to `Display (<n>)` when the display has no
name and to a bare `Display` when there is only one.

### `compactType`

`compactType` replaces the whole output with one line listing every display. Only four values are
accepted by the parser (`display.c:216-223`); the value is stored as a bit set, which is why the
last two combine two flags.

| Value | Resolution shown | Refresh rate | Example |
|---|---|---|---|
| `"none"` (default) | – | – | the two-line output above |
| `"original"` | the configured mode | no | `Display: 3456x2234 3840x2160` |
| `"scaled"` | 96-DPI normalised | no | `Display: 1728x1117 1920x1080` |
| `"original-with-refresh-rate"` | the configured mode | yes | `Display: 3456x2234 @ 120 Hz, 3840x2160 @ 60 Hz` |
| `"scaled-with-refresh-rate"` | 96-DPI normalised | yes | `Display: 1728x1117 @ 120 Hz, 1920x1080 @ 60 Hz` |

There is no `null` handling difference: `"compactType": null` resets it to `"none"`.

Two things surprise people here. The compact layouts **ignore `format` entirely** — the compact
branch returns before the format string is ever read (see Pitfalls). And `scaled` is not the
resolution your desktop reports; it is the physical mode re-divided by the DPI, normalised to
96 DPI:

```c
scaledWidth = (width * 96 + dpi / 2) / dpi;   // display.c:38-39, :335-336
```

On a HiDPI panel whose compositor reports a fractional scale (1.5x, 2.5x), that number will not
match what the desktop shows. `"compactType": "original"` is the only way to get the physical mode
back.

### `order`

`order` sorts the display list by name, ascending or descending, with `ffStrbufComp()`. Both the
console output and the JSON result follow it. The sort is applied to a local copy of the detection
result, so no other module sees the reordered list — see Pitfalls.

## Format string

Run `fastfetch -h display-format` for the authoritative list. Descriptions ending in `*` are also
available in the module `key` format string.

| Variable | Description |
|---|---|
| `{width}` | Configured width in pixels |
| `{height}` | Configured height in pixels |
| `{refresh-rate}` | Refresh rate in Hz, rounded unless `preciseRefreshRate` is set. Empty when unknown |
| `{scaled-width}` | 96-DPI normalised width |
| `{scaled-height}` | 96-DPI normalised height |
| `{name}` | Display name * |
| `{type}` | `Built-in` or `External`, empty when unknown * |
| `{rotation}` | Rotation in degrees |
| `{is-primary}` | `true` for the primary display |
| `{physical-width}` | Physical width in millimetres |
| `{physical-height}` | Physical height in millimetres |
| `{inch}` | Physical diagonal in inches, rounded to a whole number |
| `{ppi}` | Pixels per inch, rounded; `0` when the physical size is unknown |
| `{bit-depth}` | Bits per colour channel |
| `{hdr-enabled}` | `true` when HDR is currently enabled |
| `{hdr-compatible}` | `true` when the display supports HDR, whether or not it is on |
| `{manufacture-year}` | Year of manufacture, `0` when unknown |
| `{manufacture-week}` | Week of manufacture, `0` when unknown |
| `{serial}` | Serial number, empty when unknown |
| `{platform-api}` | Which backend produced this entry (see Implementation) |
| `{scale-factor}` | `dpi / 96`, formatted with `display.fraction.ndigits` |
| `{preferred-width}` | Preferred width in pixels |
| `{preferred-height}` | Preferred height in pixels |
| `{preferred-refresh-rate}` | Preferred refresh rate in Hz, empty when unknown |
| `{dpi}` | The DPI value as detected |

Inside `key` the available variables are `{index}`, `{name}`, `{type}`, `{icon}` and
`{module-name}`.

## JSON output

```jsonc
{
    "type": "Display",
    "result": [
        {
            "id": 1,
            "name": "Color LCD",
            "primary": true,
            "output": { "width": 3456, "height": 2234, "refreshRate": 120.0, "drrStatus": null, "dpi": 192 },
            "scaled": { "width": 1728, "height": 1117 },
            "preferred": { "width": 3456, "height": 2234, "refreshRate": 120.0 },
            "physical": { "width": 344, "height": 223 },
            "rotation": 0,
            "bitDepth": 10,
            "hdrStatus": "Supported",
            "type": "Builtin",
            "manufactureDate": null,
            "serial": "0x12345678",
            "platformApi": "CoreGraphics"
        }
    ]
}
```

Notes on the shape:

- `result` is an **array**, one object per display, in detection order unless `order` is set.
- `refreshRate` is always a float and is always present; `0.0` means unknown. The console output
  suppresses it in that case, the JSON does not.
- `drrStatus` is `null` on almost every platform. `"Enabled"`/`"Disabled"` come from the Windows
  and Wayland paths only.
- `hdrStatus` is one of `"Unsupported"`, `"Supported"`, `"Enabled"` or `null`. `"Supported"` does
  not mean HDR is on — `hdr-enabled` is the field that means that.
- `manufactureDate` is `null` unless EDID was parsed, in which case it is
  `{ "year": …, "week": … }`. `serial` is `null` when unknown.
- `type` is `"Builtin"` / `"External"` / `"Unknown"` — note the lowercase `i`, unlike the
  `Built-in` spelling used in the console output and in `{type}`.
- `id` is a platform handle, not an index: a `CGDirectDisplayID` on macOS, an `HMONITOR` on
  Windows, a DRM connector id on Linux, a `BScreen` id on Haiku, and `0` on several paths.
- `platformApi` is a free-form string naming the backend that produced the entry. It is the only
  reliable way to tell whether a value came from Wayland, X11, DRM or sysfs.
- `compactType`, `preciseRefreshRate` and `order` are not reflected in the JSON at all.

## Examples

```jsonc
// One line, physical modes, refresh rates
{ "type": "display", "compactType": "original-with-refresh-rate" }
```

```jsonc
// Keep the physical mode but drop the refresh rate, and sort by name
{ "type": "display", "compactType": "original", "order": "asc" }
```

```jsonc
// A custom line, with the backend that answered
{ "type": "display", "format": "{name}: {width}x{height} @ {refresh-rate}Hz via {platform-api}" }
```

```jsonc
// Only the primary display, named by the key instead of by the module
{ "type": "display", "key": "Screen {index}", "format": "{is-primary} {ppi}ppi" }
```

## Pitfalls

- **The compact layouts ignore `format`.** The whole `compactType != none` block ends in
  `return true`, so `moduleArgs.outputFormat` is never read. No warning is
  printed, and `"compactType": "scaled"` with `"format": "FMT:{width}x{height}"` still prints
  `Display: 1728x1117 1920x1080`. Reading `compactType` as "just a layout change" is wrong — it
  also disables every format variable.
- **`display.freq.spaceBeforeUnit` behaves the same in both layouts.** Compact and normal both test
  `!= FF_SPACE_BEFORE_UNIT_NEVER`, so `"default"` and `"always"` print `120 Hz` and only `"never"`
  prints `120Hz`:

  | `display.freq.spaceBeforeUnit` | normal layout | compact layout |
  |---|---|---|
  | `"default"` | `120 Hz` | `120 Hz` |
  | `"always"` | `120 Hz` | `120 Hz` |
  | `"never"` | `120Hz` | `120Hz` |
- **Other modules always see detection order, not `order`.** `ffConnectDisplayServer()` returns a
  pointer to a process-wide static, and `Display` sorts a local copy of it rather than the shared
  list. So `Monitor`, which prints the same list, does not follow `Display`'s `order`; neither does
  `Brightness`, which pairs its entries with the display list positionally on Linux and Windows.
  Set `order` on `Monitor` too if you want the two lines to match.
- **`{ppi}` is `0` when the physical size is unknown.** `ppi` is derived from `{inch}`, which is
  derived from the EDID physical size. A virtual or remote display, or one whose EDID is not
  readable, reports `0`.
- **`@ <n>x` is a scale factor, not a resolution.** It is only printed when the detected DPI
  differs from 96, and it is `dpi / 96` — so a 1.5x desktop prints `@ 1.5x`, and a display whose
  DPI could not be determined prints nothing rather than `@ 1x`.
- **The `*` primary marker needs at least two displays.** `display.c:136` gates it on
  `moduleIndex > 0`, which is `0` when there is only one display. A single-display machine never
  shows it, even though `primary` is `true` in the JSON.
- **`{type}` and `{name}` can be empty.** Both are marked `*` in `-h display-format` for that
  reason. `{type}` is empty when the backend could not classify the display, which is common on
  the DRM path.
- **`{hdr-compatible}` and `{hdr-enabled}` are different questions.** `"Supported"` in the JSON
  means the panel can do HDR; only `"Enabled"` means it is on, and only `"Enabled"` produces the
  `[HDR]` marker in the default output.
- **`hdrStatus` is `"Unsupported"` rather than `null` when EDID says so.** `null` means "not
  determined". A backend that does not read EDID at all leaves it `null`, so the two are not
  interchangeable.
- **`serial` can be a non-string-looking value.** EDID serials are often a hex word
  (`"0x12345678"`) on panels that do not carry a textual one. It is a string either way.
- **The compact layout's separator is positional, not configurable.** With
  `compactType: original` the displays are joined by a space and there is no way to change it;
  with a `-with-refresh-rate` value they are joined by `", "`. A trailing separator is trimmed
  (`display.c:57-58`).

## Implementation

### Shared layer

`ffConnectDisplayServer()` (`src/detection/displayserver/displayserver.c:93`) returns a pointer to
a file-scope static held by an `FFcacheEntry` named `displayServer`. The entry is built on first
use by calling the platform's `ffConnectDisplayServerImpl()`, and destroyed — including every
display's `name` and `serial` strbuf — when `ffCacheInvalidateAll()` runs at a
`--dynamic-interval` round boundary. In other words the whole subsystem is re-detected on every
round of `--dynamic-interval`, and cached for the whole run otherwise.

Every backend appends through `ffdsAppendDisplay()` (`displayserver.c:4`), which is the single
place where the contract is enforced:

- A display with `width == 0` or `height == 0` is dropped and `nullptr` is returned.
- `display->dpi = dpi ?: 96; // 0 means unknown` (`:29`) — a backend that cannot report a DPI gets
  96, so `scale-factor` is `1.0` and `scaled` equals the configured mode. This is also why the
  `scaled-width`/`scaled-height` division can never divide by zero.
- `name` is **moved**, not copied (`ffStrbufInitMove`, `:34`); callers must not destroy it
  afterwards.
- `bitDepth`, `hdrStatus`, `manufactureYear`, `manufactureWeek`, `serial` and `drrStatus` are
  reset to their unknown values, so a backend that has EDID data fills them in *after* the call —
  which is exactly what the X11, DRM and macOS paths do.

The result struct also carries the WM and DE fields (`wmProcessName`, `wmPrettyName`,
`wmProtocolName`, `deProcessName`, `dePrettyName`). They are filled by the same detection — on
Linux by `ffdsDetectWMDE()` (`linux/wmde.c`) — which is why `DE`, `WM`, `Icons`, `Theme`,
`Cursor`, `Font`, `Wallpaper` and `TerminalFont` all trigger the same detection as `Display`.

### Linux, the BSDs and Solaris

`ffConnectDisplayServerImpl()` (`linux/displayserver_linux.c:43`) tries, in order, stopping at the
first backend that produces at least one display:

| Order | Backend | File | `platformApi` values |
|---|---|---|---|
| 1 | Wayland | `wayland/global-output.c`, `kde-output.c` | `wayland-base`, `wayland-zxdg`, `wayland-wpcolor`, `wayland-zxdg+wpcolor`, `wayland-kde` |
| 2 | XCB + RandR | `xcb.c` | `xcb-randr-mode`, `xcb-randr-crtc`, `xcb-randr-monitor`, `xcb-randr-screen`, plus `-emu-` variants under XWayland |
| 3 | Xlib + RandR | `xlib.c` | `xlib-randr-mode`, `xlib-randr-crtc`, `xlib-randr-monitor`, `xlib-randr-screen`, plus `-emu-` variants |
| 4 | DRM ioctls | `drm.c` | `libdrm` |
| 5 | sysfs | `drm.c` | `sysfs-drm` |

Steps 1–3 are skipped entirely when `general.dsForceDrm` is set. The `-emu-` suffix means XWayland:
RandR reports a single emulated output, so the per-CRTC and per-mode paths are distinguishable from
the real ones.

The X11 backends read EDID from the output's RandR property and fill in `hdrStatus` (via
`ffEdidGetHdrCompatible()`), `manufactureYear`/`manufactureWeek` and `serial`. On X11 the rotation
case needs care: when `rotation` is 90 or 180 and RandR is *not* emulated, width and height are
swapped after the fact (`xcb.c:234-239`), because XWayland already swaps them itself.

The DRM path needs no display server. `drm.c` first walks `/sys/class/drm/*/modes` (the preferred
mode, which is why the entry is reported as `sysfs-drm`), and falls back to opening the DRM device
and issuing `DRM_IOCTL_MODE_GETRESOURCES` / `GETCONNECTOR` / `GETCRTC` (`libdrm`). On the `libdrm`
path the name comes from the connector type plus index (`eDP-1`, `HDMI-A-1`, …) unless EDID
supplies a real name, and the display type is derived from the connector type: `eDP` and `LVDS` are
`Built-in`, `HDMI-A`, `HDMI-B` and `DisplayPort` are `External`, everything else is `Unknown`.

FreeBSD adds one more fallback after DRM: if there is still no display, `kenv` is consulted for
`screen.width` and `screen.height` (`displayserver_linux.c:67-81`), which is how a plain text
console gets a resolution. The entry is reported with `platformApi` `kenv`.

After the display list is settled, `ffdsDetectWMDE()` fills in the WM/DE fields, but only when the
session is not a bare TTY.

### Android

`displayserver_android.c` has three routes, tried in order, and they differ in both coverage and
privileges:

| Route | Command | `platformApi` | Availability |
|---|---|---|---|
| 1 | `/system/bin/cmd display get-displays` | `cmd` | Android 13+. No permission needed, so it works for an app UID |
| 2 | `/system/bin/dumpsys display` | `dumpsys` | Every release, but gated behind `android.permission.DUMP` — `adb shell` and root only |
| 3 | `getprop` | `getprop` | MiUI specific, last resort |

`cmd` and `dumpsys` print the same `DisplayInfo` records, so one parser serves both; only the
command and the marker in front of each record differ (`Display id ` versus `mBaseDisplayInfo=`).
The record layout has changed across Android releases and every difference is accepted rather than
version-checked — the mode list is `modes [...]` up to Android 14 and `supportedModes [...]` from
15, for instance. The child process is detached from stdin because `cmd` forwards its stdin to the
binder service and the kernel rejects the transaction when that fd is a terminal.

Android's build block deliberately omits `drm.c` — there are no DRM device nodes to read.

### macOS

`displayserver_apple.c` uses CoreGraphics: `CGGetOnlineDisplayList()`, then
`CGDisplayCopyDisplayMode()` / `CGDisplayModeGetWidth()` / `CGDisplayModeGetHeight()` for the
pixel mode and `CGDisplayModeGetRefreshRate()` for the rate. The DPI is computed from the pixel
height over the point height, and `platformApi` is always `CoreGraphics`.

`CGDisplayRotation()` supplies the rotation. On macOS 10.11 and later the bit depth comes from the
mode's pixel encoding, on older systems from `CGDisplayModeCopyPixelEncoding()`. The built-in
panel is recognised by `CGDisplayIsBuiltin()`; the name comes from the display's product
dictionary. HDR status is filled from EDID where the panel provides it.

The DDC/CI path that `Brightness` uses on macOS lives in the brightness subsystem, not here — this
module only reports what CoreGraphics knows.

### Windows

`displayserver_windows.c` enumerates monitors with `EnumDisplayMonitors()`, then resolves each one
through `GetDisplayConfigBufferSizes()` / `QueryDisplayConfig()` /
`DisplayConfigGetDeviceInfo()`, which is what provides the target name, the refresh rate as a
rational number, and the rotation. Width and height are swapped when the rotation is 90 or 270
(`displayserver_windows.c:132-137`), so the reported mode is always in the panel's own
orientation. The system DPI comes from `GetDeviceCaps(hdc, LOGPIXELSX)`, defaulting to 96 when the
device context cannot be obtained. `platformApi` is always `GDI`.

### Haiku

`displayserver_haiku.cpp` iterates `BScreen` objects. The refresh rate is computed from the mode
timing rather than read directly:

```c
(double) mode.timing.pixel_clock * 1000 / (mode.timing.v_total * mode.timing.h_total)
```

`platformApi` is `BScreen`. The manufacturer week and year come from the monitor info, which is
only available on some panels.

### What is not reported anywhere

`drrStatus` (dynamic refresh rate) is only filled by the Windows and Wayland paths; on every other
backend it stays unknown and serialises as `null`. `bitDepth` is filled by the X11, DRM and macOS
paths. On the Android routes almost every field other than size, density-derived DPI, refresh rate
and rotation is left at its default, which is why Android entries have `hdrStatus: null`,
`serial: null` and `manufactureDate: null`.
