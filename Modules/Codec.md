# Codec

> Print hardware video acceleration codec types (decode / encode)

| | |
|---|---|
| Module type | `codec` |
| Default order | 38 (only used by `--gen-config`) |
| Module source | `src/modules/codec/codec.c` |
| Detection source | `src/detection/codec/` |

Prints **two lines per result entry** — the encoder line first, then the decoder line — and one
result entry per GPU when `splitGPU` is on, or a single merged entry when it is off. The key carries
the direction, and the GPU name too when `splitGPU` is on:

```
Codec (Encoder): H.264, HEVC / H.265, AV1
Codec (Decoder): MPEG-2, DivX / Xvid, H.264, VC-1, HEVC / H.265, VP9, AV1
```

`Codec (Encoder - NVIDIA GeForce RTX 4060)` is the `splitGPU` form. The list is the recognised
formats joined with `, ` in the fixed order of the internal bit enum — H.261, H.263, MJPEG, MPEG-1,
MPEG-2, DivX / Xvid, H.264, WMV-8, WMV-9, VC-1, VP8, HEVC / H.265, VP9, AV1, VVC / H.266,
Dolby Vision (HEVC), Apple ProRes, Apple ProRes RAW — never alphabetical and never sorted by how
likely a format is to be used. A direction with nothing to report prints `None`.

The module never appends a line number: it hands `0` to `ffPrintLogoAndKey()` in both the merged
and the split path, so three GPUs with `splitGPU` produce six lines whose only distinguishing mark
is the GPU name inside the key. Using `{index}` in a custom `key` is the way to get a counter back.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `codec_linux.c` | VA-API over DRM or X11, then VDPAU |
| Android | `codec_android.c` | NDK `AMediaCodec`, needs API 28 for the codec name |
| FreeBSD | `codec_linux.c` | Same VA-API code |
| NetBSD | `codec_linux.c` | Same VA-API code |
| OpenBSD | `codec_linux.c` | Same VA-API code |
| Solaris / illumos | `codec_linux.c` | Same VA-API code |
| Haiku | `codec_linux.c` | Same VA-API code |
| GNU/Hurd | `codec_linux.c` | Same VA-API code |
| macOS | `codec_apple.c` | VideoToolbox |
| Windows | `codec_windows.cpp` | D3D12 Video, D3D11VA and Media Foundation MFTs |

Every platform has an implementation — this module has no `codec_nosupport.c`. The shared
`codec_linux.c` compiles to a stub message only when the build lacks both VA-API and VDPAU headers.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `splitGPU` | boolean | `false` | Print one pair of lines per GPU instead of merging all GPUs into one pair |
| `useVulkan` | boolean | `false` | Detect through Vulkan video queues instead of the platform API |
| `showType` | string | `both` | `both`, `encoder` or `decoder` — which directions to detect at all |
| `key` | string | `Codec (Encoder)` / `Codec (Decoder)` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

`showType` is real work saved, not just output filtering: the detectors consult it before querying
anything, so `decoder` skips the encoder enumeration entirely. A value outside the three is
reported as `Invalid showType value: Invalid enum string` — the message repeats the option name in
place of listing the alternatives — and the module then runs with the default `both`. An unknown key
is reported as `Unknown JSON key <name>`. Both messages go through `ffPrintError()`, so both are
invisible unless `display.showErrors` is on.

## Format string

Run `fastfetch -h codec-format` for the authoritative list. The two variables marked `*` are also
available in the key format.

| Variable | Description |
|---|---|
| `{gpu}` | GPU name * — empty unless `splitGPU` is on |
| `{direction}` | `Encoder` or `Decoder` * |
| `{types}` | Comma-separated codec list, in the fixed order above |
| `{platform-api}` | API the numbers came from: `D3D12VA`, `D3D11VA+MFT`, `D3D11VA`, `VA-API (DRM)`, `VA-API (X11)`, `VDPAU`, `VideoToolbox`, `AMediaCodec` or `Vulkan Video` |

Notes that save a round of debugging:

- **`{gpu}` is empty in the default configuration.** The merged path builds an empty placeholder
  buffer for the GPU name and never fills it, so a format of `[{types}|{gpu}]` prints a trailing
  empty field until `splitGPU` is enabled. The JSON is unaffected — see below.
- **In the key format only `{gpu}`, `{direction}` and the injected `{index}` / `{icon}` /
  `{module-name}` resolve.** `{types}` and `{platform-api}` do not; an unknown placeholder is copied
  verbatim, so `"key": "{direction} {types}"` prints a literal `{types}`.
- **Conditional blocks treat `0` as unset.** `formatArgSet()` requires an integer argument to be
  `> 0`, so `{?index}…{?}` never fires for a single GPU result; `{?gpu}…{?}` fires only with
  `splitGPU`, and `{?types}…{?}` only for a direction that has something to list.
- **A custom `format` loses the `None` fallback.** The default path prints `None` for an empty
  direction; the format path leaves `{types}` empty instead, so `{direction}: {types}` can print
  `Decoder:` with nothing after it.

## JSON output

```jsonc
[
    {
        "type": "Codec",
        "result": [
            {
                "gpu": "NVIDIA GeForce RTX 4060",
                "encoders": ["H.264", "HEVC / H.265", "AV1"],
                "decoders": ["MPEG-2", "DivX / Xvid", "H.264", "VC-1", "HEVC / H.265", "VP9", "AV1"],
                "platformApi": "D3D12VA"
            }
        ]
    }
]
```

Five things are worth stating explicitly, because they differ from the text output:

- **The JSON is always per-GPU.** `splitGPU` only changes the text: with the default
  `splitGPU: false` the text collapses every GPU into one pair of lines and reports an empty
  `{gpu}`, while the JSON still carries one object per GPU with its own name.
- **`showType` does apply to the JSON.** A disabled direction is never detected, so its array is
  present but empty (`"decoders": []`) rather than absent.
- **The lists are arrays of strings**, not the joined string the format variables produce, and their
  order is the same fixed codec order.
- **The JSON can list `Unknown` where the text cannot.** The text join starts at the H.261 bit and
  the JSON loop starts one bit earlier, at the internal "unknown format" flag. A backend that sees a
  profile it cannot map — the D3D11VA path does this for any DXVA profile outside its table — sets
  that flag, so `"decoders": ["Unknown", …]` can appear for a codec the printed line silently omits.
- **An empty result is not an error here.** A detector failure writes
  `{ "type": "Codec", "error": "…" }`, but a successful detection that found no hardware
  acceleration writes `"result": []` with no `error` at all — the `No hardware video acceleration
  found` message belongs to the print path alone.

## Examples

```jsonc
// The default lines, with the API that produced them appended
{ "type": "codec", "format": "{types} [{platform-api}]" }
```

```jsonc
// One pair of lines per GPU, and the GPU name moved into the key
{ "type": "codec", "splitGPU": true, "key": "{gpu} {direction}", "format": "{types}" }
```

```jsonc
// Decode support only, no key at all — useful to diff two machines
{ "type": "codec", "showType": "decoder", "key": " ", "format": "{types}" }
```

```jsonc
// What the same machine reports through Vulkan instead of the platform API
{ "type": "codec", "useVulkan": true, "format": "{types} [{platform-api}]" }
```

The Vulkan run is worth trying once: on the machine this page was written against it returned
`H.264, HEVC / H.265, VP9, AV1 [Vulkan Video]` for decoding against the seven formats
`D3D12VA` reported, because the Vulkan video queue only advertises H.264, HEVC, AV1 and VP9.

## Pitfalls

- **A merged run hides half the data.** With `splitGPU: false` the entries are OR-ed together and
  `platformApi` is overwritten by each entry in turn, so the reported API is the **last** GPU's, not
  the first and not a combination. Two GPUs that use different APIs (a discrete card plus an
  integrated one on Linux, say) cannot be told apart from the text.
- **`Codec (Encoder): None` is normal.** A result entry is kept when *either* direction has
  something to report, and both lines are printed whenever `showType` includes both directions — so
  a decode-only GPU prints an encoder line reading `None`, and an encode-only GPU prints a decoder
  line reading `None`. Neither is an error and neither is hidden.
- **`useVulkan: true` is not a fallback.** It replaces the platform detector instead of adding to
  it, and a Vulkan detection that finds nothing produces an empty result rather than falling back to
  D3D12VA / VA-API / VideoToolbox. On a build without Vulkan support the module reports
  `Fastfetch was built without Vulkan support`, and on a Vulkan implementation older than the video
  queue extensions, `Vulkan video queue extensions are not supported by this Vulkan implementation`.
- **On Windows 11 the D3D11VA path is usually dead code.** `D3D12VA` is tried first, and as soon as
  it produces a single entry the function returns — Media Foundation is never consulted. That is why
  a Windows 11 run reports `D3D12VA` and an encoder list limited to H.264, HEVC and AV1, while a
  Windows 10 run reports `D3D11VA+MFT` and an encoder list taken from the hardware MFTs the driver
  registers. `MFTEnum2` is loaded lazily because it does not exist on Windows 8.1, which is what the
  `+MFT` suffix and the plain `D3D11VA` value distinguish.
- **Software adapters are skipped on every GPU-based backend.** The Windows path drops any DXGI
  adapter with `DXGI_ADAPTER_FLAG_SOFTWARE` (Microsoft Basic Render Driver, WARP), and a GPU that
  reports neither direction is dropped as well — so a machine whose only adapter is the software one
  reports `No hardware video acceleration found` rather than an empty codec list.
- **On Linux, a VA-API driver that initialises but supports nothing stops VDPAU from being tried.**
  VA-API over DRM is attempted first, then over X11; the first one that initialises makes the
  function return successfully, even when it added no result entry. VDPAU only runs after both fail,
  so an installed-but-crippled `libva` can hide a working VDPAU setup. VDPAU is decode-only as well:
  with `showType: encoder` it answers `VDPAU only supports decoding`.
- **On Android the codec name decides what counts as hardware.** Anything whose implementation name
  starts with `OMX.google.`, `c2.android.`, `OMX.ffmpeg.` or `OMX.PV.` is treated as software and
  excluded; the check needs `AMediaCodec_getName()`, which is API 28, so on Android 9 and older the
  module reports `AMediaCodec_getName() requires Android 9 (API 28)` instead of guessing. The
  reported GPU name is the hard-coded string `Default` on Android and macOS.
- **On macOS the API string is `VideoToolbox` and the GPU name is `Default`.** Decoders come from
  `VTIsHardwareDecodeSupported()` per codec type and encoders from `VTCopyVideoEncoderList()` filtered
  on `IsHardwareAccelerated`; nothing here is tied to a specific GPU, so a Mac with two GPUs reports
  one merged entry regardless of `splitGPU`.
- **A typo'd key or an invalid `showType` corrupts `--format json` when `display.showErrors` is
  on.** `ffPrintError()` writes to stdout, so the diagnostic line lands before the JSON array and
  the output stops being parseable. Keep `showErrors` off in anything that consumes the JSON.

## Implementation

`ffPrintCodec()` and `ffGenerateCodecJsonResult()` each call `ffDetectCodec()` themselves, which
dispatches to `ffDetectCodecVulkan()` when `useVulkan` is set and to `ffDetectCodecNative()`
otherwise; a Vulkan build without the option enabled still uses the native path. Nothing is cached,
so `--dynamic-interval` re-runs the full detection every round. Each `FFCodecResult` holds a GPU
name, the two bitmasks and the API string.

In the merged path the two bitmasks are OR-ed across all entries and `platformApi` is assigned from
each entry in turn, so the last one wins; the GPU name of the merged entry is the empty buffer that
makes `{gpu}` blank. In the split path the index is `result.length == 1 ? 0 : i + 1`, which reaches
only a custom `key`, because `ffPrintLogoAndKey()` is always called with `0`.

### Windows

`dxgi.dll` supplies the adapter list, and both backends walk it with a helper that skips
`DXGI_ADAPTER_FLAG_SOFTWARE`. Every adapter that reports at least one direction becomes an entry.

- **D3D12 Video** (Windows 11 and later, `d3d12.dll`): `D3D12CreateDevice()` →
  `ID3D12VideoDevice`. Decoding is probed with a fixed list of twenty `D3D12_VIDEO_DECODE_PROFILE_*`
  GUIDs through `D3D12_FEATURE_VIDEO_DECODE_SUPPORT` at 1920×1080, 30 fps, 10 Mbit/s, and encoding
  with the three `D3D12_VIDEO_ENCODER_CODEC_*` values through
  `D3D12_FEATURE_VIDEO_ENCODER_CODEC`. The API string is `D3D12VA`.
- **D3D11VA** (fallback, `d3d11.dll`): `D3D11CreateDevice()` with `D3D11_CREATE_DEVICE_VIDEO_SUPPORT`,
  then `ID3D11VideoDevice::GetVideoDecoderProfile()` for every profile the driver lists. A profile is
  counted only when one of the ten native DXGI formats (`NV12`, `P010`, `P016`, `YUY2`, `Y210`,
  `Y216`, `AYUV`, `Y410`, `Y416`, `420_OPAQUE`) is accepted for it by
  `CheckVideoDecoderFormat()`, which is what keeps post-processing-only modes such as `DXVA_*_IDCT`
  out of the list.
- **Media Foundation encoders**: `MFTEnum2()` with `MFT_ENUM_FLAG_HARDWARE |
  MFT_ENUM_FLAG_SORTANDFILTER`, filtered by the adapter's LUID through the
  `MFT_ENUM_ADAPTER_LUID` attribute, for the three output subtypes H.264, HEVC and AV1. The API
  string becomes `D3D11VA+MFT` when `MFTEnum2` could be loaded and `D3D11VA` when it could not.

### Linux, FreeBSD, NetBSD, OpenBSD, Solaris, Haiku, GNU/Hurd

`FF_SUPPRESS_IO()` is called before anything else. libva is loaded dynamically and its
`vaInitialize` / `vaMaxNumProfiles` / `vaMaxNumEntrypoints` / `vaQueryConfigProfiles` /
`vaQueryConfigEntrypoints` / `vaQueryVendorString` / `vaGetConfigAttributes` symbols are used
directly.

- **VA-API over DRM** (`libva-drm`): every `/dev/dri/renderD*` node is opened read-write and passed
  to `vaGetDisplayDRM()`; the first display that initialises and yields at least one format is used
  and the API string is `VA-API (DRM)`.
- **VA-API over X11** (`libva-x11` + `libX11`): `vaGetDisplay(XOpenDisplay(NULL))`, API string
  `VA-API (X11)`.
- A profile counts only when a matching entry point (`VLD`, `IDCT`, `MoComp` for decode;
  `EncSlice`, `EncSliceLP`, `FEI` for encode) reports a usable `VAConfigAttribRTFormat`. The GPU
  name is the driver vendor string, not the card model. The profile table is keyed by the numeric
  `VAProfile` values rather than their names, because the numbers are stable across driver versions
  while the `VAProfile*` identifiers are not.
- **VDPAU** (`libvdpau` + `libX11`): `vdp_device_create_x11()` on the default screen, then
  `VdpDecoderQueryCapabilities()` for a fixed table of 38 decoder profiles. Decode only, and the GPU
  name is `$VDPAU_DRIVER` when set, otherwise `Default`.

### macOS

`VTIsHardwareDecodeSupported()` is asked about a fixed table of 22 `CMVideoCodecType` four-character
codes (`h263`, `jpeg`, `dmb1`, `mp1v`, `mp2v`, `mp4v`, `avc1`, `hvc1`, `muxa`, `dvh1`, `dish`,
`deph`, `vp09`, `av01` and the six ProRes variants), with
`VTRegisterSupplementalVideoDecoderIfAvailable()` called for each on SDKs that declare it. Encoders
come from `VTCopyVideoEncoderList()` with the `IsHardwareAccelerated` flag set.

### Android

`AMediaCodec_createDecoderByType()` and `AMediaCodec_createEncoderByType()` are called per MIME type
(`video/3gpp`, `video/h263`, `video/mjpeg`, `video/mpeg2`, `video/mp2v-es`, `video/mp4v-es`,
`video/avc`, `video/hevc`, `video/x-vnd.on2.vp8`, `video/x-vnd.on2.vp9`, `video/av01`,
`video/vvc`), each codec is queried for its implementation name and deleted again, and only
non-software implementations are counted.

### Vulkan

`useVulkan` bypasses the native backend entirely. An instance is created, every physical device is
enumerated, and `VK_KHR_video_decode_queue` / `VK_KHR_video_encode_queue` are looked for in the
device extensions before the queue families are inspected. The codec sets are decoded from the
`videoCodecOperations` field of `VkQueueFamilyVideoPropertiesKHR` with hard-coded operation bits —
H.264, H.265, AV1 and VP9 for decode, H.264, H.265 and AV1 for encode — which is why the Vulkan
answer is narrower than the platform API's. The GPU name is the Vulkan `deviceName` and the API
string is `Vulkan Video`.
