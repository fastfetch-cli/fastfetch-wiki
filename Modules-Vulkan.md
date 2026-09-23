# Vulkan

> Print the highest Vulkan version supported by the GPU

| | |
|---|---|
| Module type | `vulkan` |
| Default order | 55 (only used by `--gen-config`) |
| Module source | `src/modules/vulkan/vulkan.c` |
| Detection source | `src/detection/vulkan/vulkan.c` |

Creates a throwaway Vulkan instance, enumerates the physical devices, and reports the highest
device API version along with the driver that provides it.

```
Vulkan: 1.1.357 - MoltenVK [1.4.2]
Vulkan: 1.3.280 - NVIDIA
Vulkan: 1.3.0 [Software only]
```

## Platform support

`ENABLE_VULKAN` is enabled by default on all ten platforms, and `src/detection/vulkan/vulkan.c`
is in the platform-independent source list — there is no per-platform Vulkan file.

| Platform | Runtime library loaded |
|---|---|
| Linux / FreeBSD / NetBSD / OpenBSD / Solaris / Haiku / GNU/Hurd / Android | `dlopen("libvulkan.so.2")` |
| Windows | `dlopen("vulkan-1.dll")` |
| macOS | `dlopen("libMoltenVK.dylib")` |

macOS has no Vulkan driver of its own; the module only produces a result there because
MoltenVK ships its own `libMoltenVK.dylib` and is found by the plain library search. A Mac
without MoltenVK installed reports an error rather than a version.

Building with `-DENABLE_VULKAN=OFF`, or with the Vulkan headers missing, yields
`fastfetch was compiled without vulkan support` on every platform.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

There are no module-specific keys.

## Format string

Run `fastfetch -h vulkan-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{driver}` | Driver name, plus the driver info in brackets when it is a single line |
| `{api-version}` | Highest device API version, `major.minor.patch` |
| `{conformance-version}` | Highest Vulkan conformance test suite version the devices claim |
| `{instance-version}` | Version of the instance the loader created, `major.minor.patch` |

`{api-version}` and `{instance-version}` are different numbers and normally disagree: the
instance version is what the *loader* provides, the API version is what the best *device*
supports. A machine can report `{api-version}` `1.1.357` with `{instance-version}` `1.4.357`.

## JSON output

```jsonc
{
    "type": "Vulkan",
    "result": {
        "apiVersion": "1.1.357",
        "conformanceVersion": "1.4",
        "instanceVersion": "1.4.357",
        "driver": "MoltenVK [1.4.2]",
        "gpus": [
            {
                "type": "Integrated",
                "vendor": "Apple",
                "name": "Apple M<n>",
                "driver": "1.4.2",
                "platformApi": "Vulkan 1.1.357",
                "deviceId": 452985351,
                "memory": {
                    "dedicated": { "total": 0, "used": null },
                    "shared": { "total": 17179869184, "used": null }
                }
            }
        ]
    }
}
```

The four version and driver strings are the ones the format variables expose, in camelCase. On
failure the object is `{ "type": "Vulkan", "error": "…" }`.

## Examples

```jsonc
{ "type": "vulkan", "key": "VK", "format": "{driver} ({api-version})" }
```

```jsonc
{ "type": "vulkan", "format": "{api-version}" }
```

## Pitfalls

- **`driver` means two different things.** At the top level it is the driver *name* with the
  info appended (`MoltenVK [1.4.2]`), and inside each GPU object it is the driver *info* alone
  (`1.4.2`). A format string and a JSON consumer reading `driver` do not get the same value.
- **`memory.dedicated.used` and `memory.shared.used` are always `null`.** The backend marks
  both as "unset" and never fills them, because Vulkan exposes no per-heap usage counter. Only
  the totals are real. The JSON writer does handle a set `used` correctly — it is simply never
  given one.
- **`memory.dedicated.total` is `0` for an integrated GPU.** The heap walk routes every heap to
  `shared` unless the device is discrete *and* the heap carries
  `VK_MEMORY_HEAP_DEVICE_LOCAL_BIT`, so the dedicated object exists but is empty. A consumer
  that prefers `dedicated` over `shared` will read zero on a laptop.
- **The driver info is dropped when it spans several lines.** `applyDriverName()` appends
  `driverInfo` in brackets only if it has no newline, which is why Android devices usually show
  a bare driver name.
- **CPU devices are dropped, and that is what `[Software only]` means.** Every
  `VK_PHYSICAL_DEVICE_TYPE_CPU` entry is skipped, so on a machine with only a software
  rasteriser the API version and the driver name both stay empty and the default output prints
  the *instance* version followed by the literal ` [Software only]`. A format string bypasses
  that branch entirely, so `{format}` output can be blank where the default output would have
  said something.
- **`type` is `Unknown` for anything that is not a known GPU kind.** The backend maps
  `VK_PHYSICAL_DEVICE_TYPE_DISCRETE_GPU` to `Discrete` and both the integrated and the virtual kind
  to `Integrated`, and leaves every other value — `VK_PHYSICAL_DEVICE_TYPE_OTHER`, or a device type
  a newer Vulkan header adds — as `Unknown`. A virtual GPU therefore looks exactly like a real
  integrated one, and an `OTHER` device is only distinguishable by the string itself. The `GPU`
  module spells the same device differently: its JSON writes `type: null` where this module writes
  `"Unknown"` (see [Modules-GPU](Modules-GPU)).
- **`memory.*` is the whole memory report — everything else is left out.** A GPU object carries only
  `type`, `vendor`, `name`, `driver`, `platformApi`, `deviceId` and `memory`, because Vulkan cannot
  answer any other question: index, core count, core usage, temperature, clock and PCIe speed are
  set to their "unset" sentinels by the backend and are not written at all. The `GPU` module writes
  those fields as `null` for the same device; here they are simply absent.
- **Duplicate devices are collapsed by `deviceID`.** Two physical devices that report the same
  `deviceID` — which happens with some virtualised and multi-GPU setups — produce a single
  entry, so `gpus` can be shorter than the real device count.
- **The device array is capped at 128, and overflow is an error.** `vkEnumeratePhysicalDevices`
  is called with a fixed 128-slot stack array; a `VK_INCOMPLETE` return is reported as a
  failure rather than truncated, so a machine with more than 128 physical devices gets an error
  instead of a partial list.
- **The `GPU` module consumes this module's device list.** If `gpu` runs first and falls back to
  Vulkan, it takes ownership of the cached device list and empties the singleton, so a later
  `vulkan` entry in the same run reports `"gpus": []`. See the GPU page.
- **Detection is cached for the whole process.** `ffDetectVulkan()` memoises into a
  function-scope static on first call, so `--dynamic-interval` reprints the first sample
  forever.

## Implementation

`ffDetectVulkan()` returns a process-wide `static FFVulkanResult` filled once behind a
`static bool initialized` flag. The module never frees it.

`detectVulkan()` loads the loader (`libvulkan.so.2`, `vulkan-1.dll` or `libMoltenVK.dylib`
depending on the platform) and resolves its entry points through `vkGetInstanceProcAddr` after
creating an instance. The instance is destroyed with `vkDestroyInstance` before returning, so
the module leaves no Vulkan state behind. A `FF_SUPPRESS_IO()` guard wraps the whole probe
because some drivers print to stdout on load.

The instance version is read before the instance exists, through `vkEnumerateInstanceVersion`
resolved with a null instance — the function only exists from Vulkan 1.1 on, so it is fetched
through `vkGetInstanceProcAddr` rather than `dlsym`. The instance is then created requesting
1.1 when the loader reports a minor version of at least 1, and 1.0 otherwise; 1.1 is needed for
`vkGetPhysicalDeviceProperties2`, which is the only way to reach the driver properties
structure. If the instance was created but the version query failed, the version is forced to
1.0 so that something is still reported when no device is found.

Physical devices are enumerated into a fixed 128-entry stack array. For each device the backend
reads the properties twice: the base `VkPhysicalDeviceProperties` for the name, `deviceID`,
`vendorID` and device type, and a `VkPhysicalDeviceDriverPropertiesKHR` structure chained
through the `pNext` of `VkPhysicalDeviceProperties2` — when that function is unavailable, the
plain properties call is used and the driver name, driver info and conformance version stay
empty. CPU devices are skipped outright. Before adding a device, the list is scanned for an
entry with the same `deviceID`; a match skips the device with a `goto next` (the fix for
upstream issue #456).

The reported driver name is taken from the device with the highest API version, since
`applyDriverName()` is only called when a device beats the current maximum. The device type comes
from a switch on `VkPhysicalDeviceType`: `VK_PHYSICAL_DEVICE_TYPE_DISCRETE_GPU` becomes
`FF_GPU_TYPE_DISCRETE`, `VK_PHYSICAL_DEVICE_TYPE_INTEGRATED_GPU` and
`VK_PHYSICAL_DEVICE_TYPE_VIRTUAL_GPU` become `FF_GPU_TYPE_INTEGRATED`, and every other value —
including `VK_PHYSICAL_DEVICE_TYPE_OTHER` — becomes `FF_GPU_TYPE_UNKNOWN`. CPU devices never reach
that switch, because they are skipped earlier in the loop.

Memory totals come from `vkGetPhysicalDeviceMemoryProperties`. The backend starts both totals
at `0`, then adds every heap's `size` to `dedicated` when the device is discrete and the heap
has `VK_MEMORY_HEAP_DEVICE_LOCAL_BIT`, or to `shared` otherwise. The two "used" fields are
explicitly set to `FF_GPU_VMEM_SIZE_UNSET` and never touched again.

Everything Vulkan cannot answer — index, core count, temperature, frequency, core usage and
PCIe speed — is set to its unset sentinel, and a memory type is never filled in. Those fields are
therefore left out of this module's GPU objects, and the module exposes no format variable for any
of them. (The `GPU` module, which can consume the same device list, writes them as `null` instead.)

The instance version, the highest device API version and the highest device conformance version
are all tracked with `ffVersionCompare()` and rendered at the end with `ffVersionToPretty()`,
which is why they are `major.minor.patch` strings rather than raw integers.

`ffGenerateVulkanJsonResult()` writes the whole `FFVulkanResult` into `result`: `apiVersion`,
`conformanceVersion`, `instanceVersion` and `driver` as strings, then `gpus` with one object per
device. Inside a GPU object it writes `type` (with `FF_GPU_TYPE_UNKNOWN` spelled `Unknown`), the
three strings from the backend, the numeric `deviceId`, and `memory.dedicated` /
`memory.shared` with `total` and `used` — a value that is still `FF_GPU_VMEM_SIZE_UNSET` becomes
`null`. Nothing else is written, because the backend leaves every other GPU field unset.
