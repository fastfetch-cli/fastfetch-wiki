# GPU

> Print GPU names, memory sizes, types, etc

| | |
|---|---|
| Module type | `gpu` |
| Default order | 36 (only used by `--gen-config`) |
| Module source | `src/modules/gpu/gpu.c` |
| Detection source | `src/detection/gpu/` |

Prints one line per graphics adapter: vendor and model, core count, clock, temperature, memory
usage, type and the API the numbers came from. The module has more platform backends than any
other, and it can fall back through four APIs when the native one produces nothing.

```
GPU: Apple M<n> (16) @ 1.30 GHz [Integrated]
GPU: NVIDIA GeForce RTX 4060 (3072) @ 2.46 GHz - 45.0°C (2.15 GiB / 8.00 GiB, 27%) [Discrete]
GPU: AMD Radeon 780M (512) @ 2.70 GHz [Integrated]
GPU: Virtual Video Controller [Discrete]
```

The key defaults to `GPU`; the index suffix is added only when more than one adapter is printed,
so a single GPU has no `(1)` in front of it.

## Platform support

The native backend is chosen at build time; everything else in this table is what it reads.

| Platform | Backend files | Source of the data |
|---|---|---|
| Linux | `gpu_linux.c`, `gpu_drm.c`, `gpu_pci.c`, `gpu_windows.c` | WSL2 DXCore first (x86_64 and aarch64 only), then `/sys/class/drm/card*/device/` when the method is `auto`, then `/sys/bus/pci/devices/*/` |
| Android | `gpu_android.c` | Nothing — the backend always returns an error so that Vulkan is used. The file only provides the thermal-zone temperature helper |
| FreeBSD | `gpu_bsd.c`, `gpu_bsddrm.c`, `gpu_drm.c`, `gpu_pci.c` | DRM render nodes when the method is `auto`, then `/dev/pci` |
| NetBSD | `gpu_nbsd.c`, `gpu_pci.c` | PCI configuration space through `/dev/pciN` ioctls |
| OpenBSD | `gpu_obsd.c`, `gpu_bsddrm.c`, `gpu_drm.c`, `gpu_pci.c` | DRM render nodes when the method is `auto`, then `/dev/pci` |
| Solaris / illumos | `gpu_sunos.c`, `gpu_pci.c` | `libdevinfo`, walking minor nodes of type `DDI_NT_DISPLAY` |
| Haiku | `gpu_haiku.c`, `gpu_pci.c` | `/dev/misc/poke` with `POKE_GET_NTH_PCI_INFO` |
| GNU/Hurd | `gpu_gnu.c`, `gpu_pci.c` | The PCI server under `/servers/bus/pci/` |
| macOS | `gpu_apple.c`, `gpu_apple.m` | IOKit `IOAccelerator` entries, then Metal for the API level, the type and the memory budget |
| Windows | `gpu_windows.c`, `gpu_windows.cpp` | `D3DKMTEnumAdapters2` (or `D3DKMTEnumAdapters` on 8.1 compatibility builds), plus DXCore for the type |

`gpu_windows.c` is compiled on Linux as well — that is where `ffGPUDetectWsl2()` lives, and it
only defines `ffDetectGPUImpl()` on Windows. It is registered in both platform blocks on purpose,
not by mistake.

Two more files are added outside the platform blocks:

| File | Platforms | Purpose |
|---|---|---|
| `gpu_nvidia.c`, `gpu_mthreads.c` | Linux, FreeBSD, Windows | NVML and MTML probing |
| `gpu_intel.c`, `gpu_amd.c` | Windows only | IGCL and ADL probing |

`gpu_pci.c` is also the home of the `pci.ids` and `amdgpu.ids` lookups used by every platform
except macOS and Windows, so a missing `pci.ids` costs you the model name, not the device.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `detectionMethod` | string | `pci` on x86_64 and i386, `auto` elsewhere | The *first* backend to try. See the note below — it is not an exclusive selector. |
| `hideType` | string or null | `null` | Hide one GPU type: `integrated`, `discrete` or `unknown`. `null` and `none` both mean "hide nothing". |
| `driverSpecific` | boolean | `false` | Load the vendor's own library for memory usage, core count, core usage, clock and PCIe link speed. |
| `temp` | boolean or object | `false` | Detect the temperature. The object form takes `green` and `yellow` thresholds, defaulting to 60 and 80 degrees Celsius. |
| `percent` | object | `{ "green": 50, "yellow": 80, "type": 0 }` | Colour thresholds and style for the percentage variables. `type: 0` means "use `display.percentType`". |
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

`detectionMethod` accepts `auto`, `pci`, `vulkan`, `opencl`, `egl-ext` and `opengl`. The values
are ordered internally, and each step of the chain is guarded by a "less than or equal" test
against the requested value, so the value you pick names the *first* backend that may run — every
later one is still tried if it produces nothing:

| Value | Backends tried, in order |
|---|---|
| `auto` | native (DRM first, then PCI), Vulkan, OpenCL, EGL_EXT, OpenGL |
| `pci` | native (PCI only), Vulkan, OpenCL, EGL_EXT, OpenGL |
| `vulkan` | Vulkan, OpenCL, EGL_EXT, OpenGL |
| `opencl` | OpenCL, EGL_EXT, OpenGL |
| `egl-ext` | EGL_EXT, OpenGL |
| `opengl` | OpenGL |

`auto` and `pci` differ only *inside* the native backend, which checks for `auto` to decide
whether to read the DRM render nodes before falling back to the raw PCI bus. Note that choosing
`vulkan` or anything later skips the native backend entirely — there is no value that means
"native only", and the only way to reach a single backend is `opengl`.

`driverSpecific` and `temp` are the only two keys that change which numbers exist; all the others
change formatting. Version strings in `{driver}` are gated by the global
`general.detectVersion` setting.

## Format string

Run `fastfetch -h gpu-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{vendor}` | GPU vendor, e.g. `NVIDIA` |
| `{name}` | GPU model name |
| `{driver}` | Driver name, with the version appended when `general.detectVersion` is on |
| `{temperature}` | Temperature, formatted with the `temp` thresholds |
| `{core-count}` | Core or shader-unit count |
| `{type}` | `Integrated`, `Discrete` or `Unknown` |
| `{dedicated-total}` | Total dedicated memory |
| `{dedicated-used}` | Used dedicated memory |
| `{shared-total}` | Total shared memory |
| `{shared-used}` | Used shared memory |
| `{platform-api}` | The API the numbers came from, e.g. `Metal 4` or `DRM (card1)` |
| `{frequency}` | Current or maximum clock, rendered in GHz |
| `{index}` | Position of this GPU in the printed list, not the JSON `index` field |
| `{dedicated-percentage-num}` | Dedicated memory usage as a percentage |
| `{dedicated-percentage-bar}` | Dedicated memory usage as a bar |
| `{shared-percentage-num}` | Shared memory usage as a percentage |
| `{shared-percentage-bar}` | Shared memory usage as a bar |
| `{core-usage-num}` | Core utilisation as a percentage |
| `{core-usage-bar}` | Core utilisation as a bar |
| `{memory-type}` | Memory type, e.g. `GDDR6`. Windows only |
| `{pcie-max-speed}` | Maximum PCIe link, as `<gen> x<lanes>` |
| `{pcie-curr-speed}` | Current PCIe link, as `<gen> x<lanes>` |

## JSON output

```jsonc
{
    "type": "GPU",
    "result": [
        {
            "index": 0,
            "coreCount": 16,
            "coreUsage": 0.0,
            "memory": {
                "dedicated": { "total": null, "used": null },
                "shared": { "total": 12713115648, "used": 26656768 },
                "type": null
            },
            "driver": "com.apple.AGXG13X 360.34.5",
            "name": "Apple M<n>",
            "temperature": null,
            "type": "Integrated",
            "vendor": "Apple",
            "platformApi": "Metal 4",
            "frequency": 1296,
            "deviceId": 4294969916,
            "pcieSpeed": null
        }
    ]
}
```

Unavailable values are `null`. `frequency` is in MHz here but is rendered in GHz by
`{frequency}`. On failure the object is `{ "type": "GPU", "error": "GPU detection failed" }`.
`hideType` filters this array exactly as it filters the text output, so a hidden GPU is absent
from `result`.

## Examples

```jsonc
{ "type": "gpu", "format": "{name} ({core-count}) - {type}" }
```

```jsonc
{ "type": "gpu", "hideType": "integrated", "format": "{vendor} {name} {dedicated-used}/{dedicated-total}" }
```

```jsonc
{ "type": "gpu", "temp": { "green": 50, "yellow": 70 }, "driverSpecific": true }
```

## Pitfalls

- **`{index}` is not the JSON `index`.** The format variable is filled from the position of the
  GPU in the printed list — `0` for a single adapter, `1`, `2`, … when several are printed —
  while the JSON field is the backend's own index. With `detectionMethod: "opengl"` on macOS the
  format string prints `idx=0` and the JSON object says `"index": null`, because the OpenGL
  fallback never sets an index.
- **`{core-count}` prints `-1` when the count is unknown.** The unset sentinel leaks straight
  into the format output; the JSON field is `null` for the same GPU. The default output hides it,
  so the two paths disagree.
- **All-hidden is an error in text mode but an empty array in JSON.** When `hideType` removes
  every GPU, the text path prints `GPUs found but all hidden by hideType option`, while
  `--format json` returns `"result": []` with no `error` field. Both agree on which GPUs exist;
  they disagree on whether that is worth reporting.
- **The default output never shows shared memory.** The parenthesised memory block is built from
  `dedicated` only, so on a machine whose GPU reports unified memory — Apple Silicon, or an AMD
  APU — the default output has no memory at all even though `{shared-total}` and `{shared-used}`
  are populated. You need a format string to see them.
- **`detectionMethod` is a starting point, not a restriction.** The chain is a series of "less
  than or equal" tests over the enum order, so `pci` still falls back to Vulkan, OpenCL, EGL_EXT
  and OpenGL when the PCI scan finds nothing. Conversely, asking for `vulkan` or later *skips*
  the native backend altogether, which is the opposite of what the name suggests. There is no
  value that means "native only".
- **`pci` is not rejected where it is documented as unsupported.** The schema says PCI detection
  is not supported on Windows and macOS, but nothing validates that: on macOS
  `detectionMethod: "pci"` returns the IOKit and Metal result unchanged.
- **`Unknown` type has three spellings.** The default output omits the `[Type]` suffix entirely,
  `{type}` renders the string `Unknown`, and the JSON writes `type: null` — while the Vulkan and
  OpenCL modules write the string `"Unknown"` for the same condition.
- **The type fallback compares vendor strings by pointer, and only four backends call it.** The
  heuristic in `ffGPUDetectTypeByVendorAndName()` matches on `gpu->vendor.chars` being the exact
  pointer returned by the vendor-id table, so it only fires where the vendor was assigned as a
  static string. NetBSD, Solaris, Haiku, GNU/Hurd and OpenBSD's PCI path never call it at all, so
  `{type}` stays `Unknown` on those platforms.
- **`{pcie-max-speed}`, `{pcie-curr-speed}` and `{memory-type}` are mostly empty.** PCIe link
  speeds are read from the sysfs link-speed files and from the vendor libraries, and the memory
  type only exists on Windows.
- **AMD memory numbers are "visible" VRAM, not total VRAM.** On Linux they come from
  `mem_info_vis_vram_total` and `mem_info_vis_vram_used`, which exclude the portion the kernel
  has reserved, so they are lower than what the vendor tool reports.
- **`driverSpecific` loads vendor libraries at runtime and fails silently.** A missing
  `libnvidia-ml.so`, `nvml.dll`, `libmtml.so`, `ControlLib.dll` or `atiadlxx.dll` simply leaves the
  extra fields empty; no error is printed.
- **The `GPU` module consumes the Vulkan and OpenCL device lists.** When it falls back to either
  of those APIs it takes ownership of the cached list, so a later `vulkan` or `opencl` entry in the
  same run reports `"gpus": []`. See [Modules-Vulkan](Modules-Vulkan) and
  [Modules-OpenCL](Modules-OpenCL).
- **`{temperature}` needs `temp: true`, and on Linux and FreeBSD the NVIDIA path also needs the
  vendor library.** The sysfs `hwmon` readers cover AMD and Intel, but an NVIDIA card on the
  proprietary driver is only read through NVML.

## Implementation

`ffPrintGPU()` and `ffGenerateGPUJsonResult()` both call `ffDetectGPU()` once and then run the
result list through the same `selectGPUs()` helper, which drops every entry whose type matches
`hideType`, so the text and JSON paths always agree on the surviving set. The text path then prints
each survivor, the JSON path writes one object per survivor. Both destroy the five string buffers
of every *detected* entry afterwards, including the filtered-out ones.

### The detection chain

`ffDetectGPU()` is a sequence of guarded steps. A step runs only when the requested method does
not sort later than it, so every step before the requested one is skipped, and the first step that
yields at least one GPU wins:

1. **Native.** `ffDetectGPUImpl()`, implemented once per platform. On Linux and FreeBSD the
   `auto` method first reads `/sys/class/drm/` and, if that produced nothing, walks the PCI bus;
   on macOS it is IOKit plus Metal; on Windows it is the D3DKMT adapter enumeration.
2. **Vulkan.** The shared Vulkan singleton is queried and its device list is *moved* into the
   result, which empties the singleton for the rest of the run.
3. **OpenCL.** Same pattern with the OpenCL singleton.
4. **EGL_EXT.** `eglQueryDevicesEXT()` is resolved at runtime from `libEGL`, so the code compiles
   everywhere the headers exist but only runs where the extension is present. Software devices
   are skipped by looking for `EGL_MESA_device_software`, and the device UUID is used as the
   device id.
5. **OpenGL.** A last resort that produces exactly one entry, with the index, core count,
   temperature, clock and PCIe speed all left unset, and all four memory fields set to the unset
   sentinel so they render as empty rather than as `0 B`. The vendor and type are guessed from the
   renderer string.

On Android step 1 is designed to fail, so the platform always lands on Vulkan; when the `temp`
option is on and exactly one GPU was found, the Vulkan branch also reads the thermal-zone
temperature.

### Where the data comes from per platform

**Linux** parses `pci:` modalias strings from the sysfs `modalias` files, then enriches each
device from the driver's sysfs directory: the driver name from the `driver` symlink, its version
from `module/version`, and — for AMD — the revision file, which feeds the `amdgpu.ids` lookup.
Memory, temperature, core usage and PCIe link speed come from `hwmon/` and the `mem_info_*` and
`gpu_busy_percent` files, gated on `temp` and `driverSpecific`. Intel clocks come from
`gt_max_freq_mhz` or, with the `xe` driver, `tile0/gt0/freq0/max_freq`. On aarch64 an Apple GPU
behind the Asahi driver is recognised and its name is translated from the `agx-t*` identifier
through the CPU model table. Device-tree systems (`of:` modalias) take a separate path that marks
the GPU integrated.

**macOS** enumerates `IOAccelerator` entries, reads `CFBundleIdentifier` as the driver,
`gpu-core-count`, and the `PerformanceStatistics` dictionary for utilisation and memory. The
model name comes from the `model` property, or from the parent entry when the accelerator itself
does not carry one — that is the Intel Iris case. Metal then supplies the API level, overrides
the type from `hasUnifiedMemory`, sets `{index}` from `locationNumber`, and replaces
`shared.total` with `recommendedMaxWorkingSetSize`.

**Windows** enumerates adapters through D3DKMT, then queries each one with `SetupAPI` for the
vendor and device ids, the LUID, the memory type and, when requested, the PCIe generation and
link width. DXCore is used to decide integrated versus discrete.

**FreeBSD, OpenBSD and NetBSD** read PCI configuration space directly through their respective
ioctls, with FreeBSD and OpenBSD additionally supporting DRM render nodes. **Solaris** uses
`libdevinfo`, **Haiku** the poke driver, and **GNU/Hurd** the PCI server; none of the three sets
a GPU type.

### Where the type comes from

Three sources, in order of precedence: the backend itself (Metal's `hasUnifiedMemory`, DXCore's
`IsIntegrated`, the IOKit vendor id, the DRM ioctl answers), then
`ffGPUDetectTypeByVendorAndName()`, then nothing — in which case the type stays `Unknown`.

The fallback heuristic is deliberately narrow: NVIDIA is discrete only for names starting with
`GeForce`, `Quadro` or `Tesla`; Moore Threads only for `MTT `; Intel is integrated only when the
device id is exactly PCI `0000:00:02.0`; VMware and Parallels are integrated. Every other
combination is left unknown rather than guessed.

It is also easy to miss that the vendor test is a **pointer** comparison against the constants
returned by `ffGPUGetVendorString()`, and that only four backends call the function at all — see
the corresponding Pitfalls entry.

### Driver-specific probing

`ffGPUDetectDriverSpecific()` is shared by Linux, FreeBSD and Windows. It maps the vendor to a
library and an entry point — NVML, MTML, IGCL or ADL — loads it lazily, and passes a condition
struct describing the device plus a result struct in which each field pointer is either the
destination or `nullptr`, so a caller can ask for exactly the fields it needs. The PCI bus id is
used as the match key, except on Windows where the LUID is available. OpenBSD excludes this path
entirely. On Linux the AMD and Intel vendors are handled before this generic path, because their
sysfs interfaces are richer than the vendor libraries'.
