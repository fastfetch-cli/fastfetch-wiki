# Camera

> Print available cameras

| | |
|---|---|
| Module type | `camera` |
| Default order | 62 (only used by `--gen-config`) |
| Module source | `src/modules/camera/camera.c` |
| Detection source | `src/detection/camera/` |

Prints one line per camera. The key is the module's display name in the active language (`Camera`,
or `摄像头` under `--key-language zh_CN`), and the value is the device name, followed by ` - ` and
the colour space when one was detected, followed by the resolution when both dimensions are
non-zero:

```
Camera: Integrated Camera - sRGB (1920x1080 px)
```

A camera that reports `0` for either dimension prints no `(WxH px)` suffix at all — never
`(0x0 px)` — and a camera without a colour space loses the ` - ` part too, leaving the bare name.

Two or more cameras get a numbered key (`Camera 1`, `Camera 2`); a single camera is printed without
a number. The module has no option that filters, sorts or limits the list, so it always prints every
camera the platform backend can find.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `camera_linux.c` | V4L2, `/dev/video0` … `/dev/video9` |
| Android | `camera_android.c` | NDK camera2 (`ACameraManager`) |
| FreeBSD | `camera_linux.c` | Same V4L2 code |
| NetBSD | `camera_linux.c` | Same V4L2 code |
| OpenBSD | `camera_linux.c` | Reaches V4L2 through `<sys/videoio.h>` |
| Solaris / illumos | `camera_nosupport.c` | Not supported |
| Haiku | `camera_nosupport.c` | Not supported |
| GNU/Hurd | `camera_nosupport.c` | Not supported |
| macOS | `camera_apple.m` | AVFoundation, `AVCaptureDeviceDiscoverySession` |
| Windows | `camera_windows.cpp` | Media Foundation, `MFEnumDeviceSources` |

The three stub platforms keep the module registered and report the standard
`Not supported on this platform` message, which is silent unless `display.showErrors` is on.

## Configuration

The module defines no keys of its own: `option.h` contains nothing but the shared `FFModuleArgs`.
Everything a camera line can be influenced by is therefore the generic set.

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

Because the parser has nothing to reject except unknown names, a typo is the only way to get an
error out of this section: with `"display": { "showErrors": true }` the key `vendor` prints
`Camera: Unknown JSON key vendor` and the module then runs normally with the typo ignored. Without
`showErrors` the same config is completely silent.

## Format string

Run `fastfetch -h camera-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{name}` | Device name |
| `{vendor}` | Vendor — empty on every platform except macOS |
| `{colorspace}` | Colour space — empty when the backend did not recognise the value |
| `{id}` | Opaque, platform-specific identifier (see below) |
| `{width}` | Width in px, `0` when unknown |
| `{height}` | Height in px, `0` when unknown |

Three details are easy to get wrong:

- **None of the six is available in the key format.** `ffPrintLogoAndKey()` parses `key` with
  `{index}`, `{icon}` and `{module-name}` only, so `"key": "{name}"` prints a literal `{name}`.
  `{index}` can be used, and follows the printed line number — `0` for a single camera, `1` for the
  first of several.
- **An unknown placeholder inside `key` is copied verbatim**, including the conditional syntax. A
  key of `"[{?index}I{index}{?}][{?vendor}V{vendor}{?}]"` prints `[][{?vendor}V{vendor}{?}]`: the
  index block is evaluated (`{?index}` cannot fire for a single camera, see the next point) while
  the vendor block is not, because `vendor` does not exist in the key namespace.
- **Conditional blocks treat `0` as unset.** `formatArgSet()` counts an integer argument as set only
  when it is `> 0`, so `{?index}…{?}` stays silent for the first (and, with one camera, only) line,
  and `{?width}…{?}` stays silent for a camera whose width is unknown. Strings and lists behave the
  obvious way: `{?vendor}…{?}` needs a non-empty vendor, `{?types}…{?}` in the Codec module needs a
  non-empty list.

The JSON field for the colour space is `colorSpace` while the format variable is `{colorspace}` —
the two spellings really do differ.

## JSON output

```jsonc
[
    {
        "type": "Camera",
        "result": [
            {
                "name": "Integrated Camera",
                "vendor": "",
                "colorSpace": "sRGB",
                "id": "\\\\?\\usb#vid_0000&pid_0000&mi_00#0&0000000&0&0000#{e5323777-f976-4f5b-9b55-b94699c46e44}\\global",
                "width": 1920,
                "height": 1080
            }
        ]
    }
]
```

`width` and `height` are numbers, not the preformatted strings the format variables deliver, and
`id` is whatever the backend uses to name the device:

| Platform | `id` |
|---|---|
| Windows | The Media Foundation symbolic link (`\\?\usb#vid_…`), empty if the device does not publish one |
| Linux, FreeBSD, NetBSD, OpenBSD | `v4l2_capability.bus_info` |
| macOS | `AVCaptureDevice.uniqueID` |
| Android | The camera2 id string (`"0"`, `"1"`, …) |

Two failure modes are not symmetric:

- The detector returning an error gives `{ "type": "Camera", "error": "…" }` with no `result` at all.
- The detector returning success with no cameras gives `"result": []` — **no** `error` field. The
  `No camera found` message exists only on the print path, so a JSON consumer that checks `error`
  cannot tell an empty machine from a working one.

## Examples

```jsonc
// The default line, rebuilt by hand
{ "type": "camera", "format": "{name}{?colorspace} - {colorspace}{?}{?width} ({width}x{height} px){?}" }
```

```jsonc
// Model and resolution only, with the number kept but the module name replaced
{ "type": "camera", "key": "CAM {index}", "format": "{name} [{width}x{height}]" }
```

```jsonc
// The identifier is the only stable handle for scripting a specific camera
{ "type": "camera", "key": " ", "format": "{id}\t{name}" }
```

## Pitfalls

- **`{vendor}` is empty on Windows, Linux and Android.** Only `camera_apple.m` fills it (from
  `AVCaptureDevice.manufacturer`); the other three backends never touch the field, so a config that
  relies on the vendor to tell two cameras apart works on macOS and silently prints nothing
  elsewhere.
- **A camera can appear without a resolution.** On Windows the entry is appended to the result list
  as soon as the friendly name has been read, and only afterwards is the Media Foundation source
  activated. If `ActivateObject()`, `CreatePresentationDescriptor()` or `GetMediaTypeHandler()`
  fails — a camera that another process holds exclusively, or a driver that is not fully started —
  the line is still printed, with an empty colour space and no `(WxH px)` part. There is no way to
  tell such a line from a genuinely low-resolution device except by its missing colour space.
- **On Windows the resolution comes from the first video media type, not the largest one.** The
  detector walks the stream's media types, takes the first one whose major type is
  `MFMediaType_Video`, reads `MF_MT_FRAME_SIZE` from it and breaks out of the loop. The comment in
  the source calls this "assume first type is the maximum resolution", but nothing verifies it, and
  the return value of `MFGetAttributeSize()` is discarded — a media type without a frame size leaves
  `0x0`.
- **A friendly name that does not fit in 256 UTF-16 code units removes the camera from the
  output.** The Windows backend reads the name into a fixed `wchar_t[256]` buffer; `GetString()`
  fails with `E_NOT_SUFFICIENT_BUFFER` for a longer name and the loop `continue`s, so an
  over-long name is indistinguishable from a camera that does not exist.
- **Windows enumerates every video-capture source Media Foundation exposes**, virtual cameras
  included (OBS, phone-link, capture cards). The module cannot hide them.
- **On Linux only `/dev/video0` … `/dev/video9` are probed, and the loop stops at the first missing
  node.** `open()` returning `ENOENT` breaks out of the loop, while `ENXIO` merely continues to the
  next index. On a machine that exposes `/dev/video1` but not `/dev/video0` — a common layout for
  cameras that also provide a metadata node — nothing is listed at all, and an 11th camera is
  invisible even when the first ten exist.
- **On Linux a node is kept only when it advertises `V4L2_CAP_VIDEO_CAPTURE`.** Metadata-only and
  codec-only nodes are skipped, which is what keeps the output free of the companion nodes some
  UVC drivers create, but it also means a device whose driver reports only
  `V4L2_CAP_VIDEO_CAPTURE_MPLANE` is skipped.
- **On Android the name is synthetic.** The backend maps `ACAMERA_LENS_FACING` to
  `builtin-front`, `builtin-back`, `builtin-external` or `Unknown`; the product name is never read,
  and `{vendor}` and `{colorspace}` are always empty. The resolution is the largest JPEG-capable
  output stream configuration, falling back to the largest configuration of any output format when
  the HAL lists no JPEG — that is the sensor's maximum, not the resolution any application is using.
- **On macOS the resolution is `activeFormat`, i.e. whatever the device currently has selected**,
  not the maximum the sensor offers; for a camera no session has configured that is the default
  format.
- **Nothing is printed when there is no camera, unless `display.showErrors` is on.** The module
  reports `No camera found` through `ffPrintError()`, which returns immediately while `showErrors`
  is `false` — so a machine without a camera loses a line rather than printing an explanatory one,
  and `--format json` still emits `"result": []`.
- **With `display.showErrors: true` a typo'd key corrupts `--format json`.** `ffPrintError()` writes
  the `Camera: Unknown JSON key x` line to **stdout** before the JSON array, so the run is no longer
  parseable. Keep `showErrors` off in anything that pipes the JSON output.

## Implementation

`ffPrintCamera()` and `ffGenerateCameraJsonResult()` both call `ffDetectCamera()` themselves and
then walk the `FFCameraResult` list; nothing is cached in between, so a `--dynamic-interval` run
re-enumerates the cameras on every round. Each result carries `name`, `vendor`, `id`, `colorspace`,
`width` and `height`. In the text path the index handed to `ffPrintLogoAndKey()` is
`result.length == 1 ? 0 : i + 1`, which is why a single camera has no number, and the JSON path
ignores the index entirely.

### Windows

Media Foundation is loaded dynamically (`mfplat.dll`, `mf.dll`) and the enumerator is
`MFEnumDeviceSources()` with `MF_DEVSOURCE_ATTRIBUTE_SOURCE_TYPE_VIDCAP_GUID`. For each source:

- `MF_DEVSOURCE_ATTRIBUTE_FRIENDLY_NAME` → `name`; a failure or an empty length skips the device.
- `MF_DEVSOURCE_ATTRIBUTE_SOURCE_TYPE_VIDCAP_SYMBOLIC_LINK` → `id` (optional).
- `ActivateObject()` → `IMFMediaSource`, then `CreatePresentationDescriptor()`, then
  `GetStreamDescriptorByIndex(0, …)` and `GetMediaTypeHandler()`. Only stream 0 is inspected, so a
  device with several video streams (an IR or depth stream next to the colour one) reports the
  first.
- The colour space comes from `MF_MT_VIDEO_PRIMARIES`: `MFVideoPrimaries_BT709` → `sRGB`,
  `MFVideoPrimaries_BT470_2_SysM` and `MFVideoPrimaries_BT470_2_SysBG` → `NTSC`,
  `MFVideoPrimaries_SMPTE170M` → `SMPTE 170M`, `MFVideoPrimaries_SMPTE240M` → `SMPTE 240M`,
  `MFVideoPrimaries_EBU3213` → `EBU 3213`, `MFVideoPrimaries_SMPTE_C` → `SMPTE C`,
  `MFVideoPrimaries_BT2020` → `BT.2020`, `MFVideoPrimaries_XYZ` → `XYZ`,
  `MFVideoPrimaries_DCI_P3` → `DCI-P3`, `MFVideoPrimaries_ACES` → `ACES`, and the value 13, which
  the source names `MFVideoPrimaries_Display_P3` in a comment → `Display P3`. Anything else,
  `MFVideoPrimaries_Unknown` included, leaves it empty.

### Linux, FreeBSD, NetBSD, OpenBSD

`/dev/videoN` is opened `O_RDONLY | O_CLOEXEC` for N = 0…9; `VIDIOC_QUERYCAP` must report
`V4L2_CAP_VIDEO_CAPTURE` and `VIDIOC_G_FMT` must succeed, otherwise the node is skipped. `cap.card`
becomes the name, `cap.bus_info` the identifier, `fmt.fmt.pix.width`/`height` the resolution, and
`fmt.fmt.pix.colorspace` is mapped with the full V4L2 names: `V4L2_COLORSPACE_SMPTE170M` →
`SMPTE 170M`, `V4L2_COLORSPACE_SMPTE240M` → `SMPTE 240M`, `V4L2_COLORSPACE_BT878` → `BT.878`,
`V4L2_COLORSPACE_470_SYSTEM_M` → `NTSC`, `V4L2_COLORSPACE_470_SYSTEM_BG` → `EBU 3213`,
`V4L2_COLORSPACE_JPEG` → `JPEG`, `V4L2_COLORSPACE_REC709` and `V4L2_COLORSPACE_SRGB` → `sRGB`,
`V4L2_COLORSPACE_OPRGB` → `Adobe RGB`, `V4L2_COLORSPACE_BT2020` → `BT.2020`,
`V4L2_COLORSPACE_RAW` → `RAW`, `V4L2_COLORSPACE_DCI_P3` → `DCI-P3`; anything else is left empty.
The `OPRGB`, `BT2020`, `RAW` and `DCI_P3` cases are written with their numeric values in the source,
with the constant name only in a comment. The vendor is never set.
Built without the V4L2 headers the backend compiles to
`Fastfetch was compiled without <linux/videodev2.h>`.

### macOS

`FF_SUPPRESS_IO()` is called first, because creating the discovery session writes to stderr on the
first use of a camera. The session asks for `AVCaptureDeviceTypeBuiltInWideAngleCamera` plus the
external type — `AVCaptureDeviceTypeExternal` when the SDK declares it, otherwise the deprecated
`AVCaptureDeviceTypeExternalUnknown`. `localizedName`, `manufacturer` and `uniqueID` fill the three
string fields, `activeColorSpace` is mapped (`sRGB`, `P3-D65`, `HLG_BT2020` → `BT2020-HLG`,
`AppleLog`, `AppleLog2`) and the resolution comes from
`CMVideoFormatDescriptionGetDimensions(device.activeFormat.formatDescription)`, with a negative
dimension folded to `0`. Two rough edges: `colorspace` is the one field the backend never
initialises before the switch, so an `activeColorSpace` outside those five values leaves a garbage
`FFstrbuf` behind, and the `No support for old MacOS version` error is emitted by a **compile-time**
SDK check (`MAC_OS_X_VERSION_10_15`), not by a runtime macOS version test.

### Android

The NDK camera2 manager is used: `ACameraManager_create()`, `ACameraManager_getCameraIdList()`, then
per camera `ACameraManager_getCameraCharacteristics()`. `ACAMERA_LENS_FACING` selects one of the
four synthetic names, the id string becomes `id`, and the resolution is the largest stream
configuration with `ACAMERA_SCALER_AVAILABLE_STREAM_CONFIGURATIONS_OUTPUT` whose format is
`HAL_PIXEL_FORMAT_JPEG` — with a second pass over all output formats when the HAL lists no JPEG.
`vendor` and `colorspace` stay empty. Every entry point used here is API 24, the level this build
targets, so none of them needs the weak-symbol guard in `common/android/api.h`.
