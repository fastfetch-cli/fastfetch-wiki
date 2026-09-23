# OpenCL

> Print the highest OpenCL version supported by the GPU

| | |
|---|---|
| Module type | `opencl` |
| Default order | 57 (only used by `--gen-config`) |
| Module source | `src/modules/opencl/opencl.c` |
| Detection source | `src/detection/opencl/opencl.c` |

Prints the version string of the newest OpenCL platform on the machine — not of a device, and
not the version fastfetch was built against.

```
OpenCL: 1.2 (Aug  8 2026 15:27:26)
OpenCL: 3.0 CUDA 12.4.131
```

## Platform support

The module is compiled on every platform; what varies is whether OpenCL support is compiled
in and whether a runtime library is present.

| Platform | `FF_HAVE_OPENCL` from CMake | Runtime lookup |
|---|---|---|
| Linux | yes (`ENABLE_OPENCL`, on by default) | `dlopen("libOpenCL.so.1")` |
| FreeBSD / NetBSD / OpenBSD | yes | `dlopen("libOpenCL.so.1")` |
| Solaris / Haiku / GNU/Hurd | yes | `dlopen("libOpenCL.so.1")` |
| Windows | yes | `dlopen("OpenCL.dll")` |
| Android | yes | `dlopen("libOpenCL.so.1")` |
| macOS | **no** — re-enabled inside the source | linked `-framework OpenCL`, called directly |

macOS is the odd one out in two ways. `ENABLE_OPENCL` is forced to `OFF` by
`cmake_dependent_option()` because `APPLE` is not in its condition list, so `ff_lib_enable()`
returns early and never defines `FF_HAVE_OPENCL`. The source then re-defines it itself:

```c
#if !defined(FF_HAVE_OPENCL) && defined(__APPLE__) && defined(MAC_OS_X_VERSION_10_15)
    #define FF_HAVE_OPENCL 1
#endif
```

and `CMakeLists.txt` links `-framework OpenCL` unconditionally for `APPLE`. The net effect is
that OpenCL works on macOS 10.15 and later **regardless of `-DENABLE_OPENCL`**.

On the other nine platforms, building without OpenCL produces a module that reports
`fastfetch was compiled without OpenCL support` rather than silently printing nothing.

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

Run `fastfetch -h opencl-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{version}` | Platform version, with a leading `OpenCL ` stripped |
| `{name}` | Platform name (`NVIDIA CUDA`, `Apple`, `Portable Computing Language`, …) |
| `{vendor}` | Platform vendor (`NVIDIA Corporation`, `Apple`, …) |

`{name}` and `{vendor}` describe the *platform*, not the device. The device names are only
reachable through the `GPU` module or through `--format json`.

## JSON output

```jsonc
{
    "type": "OpenCL",
    "result": {
        "version": "1.2 (Aug  8 2026 15:27:26)",
        "name": "Apple",
        "vendor": "Apple",
        "gpus": [
            {
                "type": "Integrated",
                "vendor": "Apple",
                "name": "Apple M<n>",
                "driver": "1.0",
                "platformApi": "OpenCL 1.2",
                "coreCount": 16,
                "frequency": 1000,
                "memory": {
                    "dedicated": { "total": null, "used": null },
                    "shared": { "total": 12713115648, "used": null }
                },
                "deviceId": 16940800
            }
        ]
    }
}
```

The JSON result carries a `gpus` array that the text output does not show at all. On failure
the object is `{ "type": "OpenCL", "error": "…" }`.

## Examples

```jsonc
{ "type": "opencl", "key": "CL", "format": "{name} {version}" }
```

```jsonc
{ "type": "opencl", "format": "{vendor} — {version}" }
```

## Pitfalls

- **`{version}` is not just a number.** It is the raw `CL_PLATFORM_VERSION` string with the
  `OpenCL ` prefix removed, and most platforms append their build date — so
  `1.2 (Aug  8 2026 15:27:26)` is a normal value. Use `--format json` and split it yourself if
  you need a comparable version.
- **The version is the highest *platform* version, not the device's.** With several OpenCL
  platforms installed (a GPU vendor's and a CPU runtime's, for example) the module keeps the
  one whose version string sorts last, and reports that platform's name and vendor. Device
  entries from the other platforms are still collected into `gpus`, so the platform fields and
  the device list can describe different runtimes.
- **`memory.dedicated.used` and `memory.shared.used` are always `null`.** The OpenCL backend
  initialises all four memory fields to "unset" and then fills in only the *total* from
  `CL_DEVICE_GLOBAL_MEM_SIZE`. A consumer that computes a percentage from `used / total` will
  divide by a null. The JSON writer does handle a set `used` correctly — it is simply never
  given one.
- **`driver` in the JSON is a version, not a name.** It is `CL_DRIVER_VERSION` with everything
  up to the first space removed, so a value of `1.0` or `12.4.131` is expected. There is no
  OpenCL equivalent of Vulkan's driver name field.
- **The `GPU` module consumes this module's device list.** If `gpu` runs first and falls back
  to OpenCL, it takes ownership of the cached device list and empties the singleton, so a later
  `opencl` entry in the same run reports `"gpus": []`. See the GPU page.
- **`--format json` on a build without OpenCL support reports an error, not an empty result.**
  The module returns `false` with `error: "fastfetch was compiled without OpenCL support"`, and
  because `display.showErrors` defaults to `false` the text path prints nothing at all.
- **Detection is cached for the whole process.** `ffDetectOpenCL()` memoises into a
  function-scope static on first call, so `--dynamic-interval` does not re-query the driver and
  a long-running fastfetch reports the first sample forever.

## Implementation

`ffDetectOpenCL()` returns a pointer to a process-wide `static FFOpenCLResult` that is filled
exactly once, guarded by a `static bool initialized`. The module never owns the result and
never frees it.

The backend is dlopen-based on every platform except macOS, where the OpenCL framework is
linked directly and the four entry points are taken by name instead of through
`FF_LIBRARY_LOAD_SYMBOL`. Both paths converge on `openCLHandleData()`, which resolves
`clGetPlatformIDs`, `clGetPlatformInfo`, `clGetDeviceIDs` and `clGetDeviceInfo` — only these
four symbols are used.

Detection walks every platform returned by `clGetPlatformIDs` (up to 32). For each one it
reads `CL_PLATFORM_VERSION` and keeps the platform whose version string compares greater than
the current best, using a plain string comparison. When a platform wins, `CL_PLATFORM_NAME` and
`CL_PLATFORM_VENDOR` are re-read for that same platform, so the three fields always describe
one runtime.

Devices are enumerated separately, with `CL_DEVICE_TYPE_GPU`, up to 32 per platform. A platform
whose `clGetDeviceIDs` fails is skipped with `continue` — the platform's version may already
have been recorded, which is why the version and the device list can disagree. For each device
the backend fills `CL_DEVICE_NAME`, `CL_DEVICE_VERSION` (into `platformApi`),
`CL_DEVICE_VENDOR_ID` (mapped to a vendor name through `ffGPUGetVendorString()`, falling back
to the `CL_DEVICE_VENDOR` string), `CL_DRIVER_VERSION`, `CL_DEVICE_MAX_COMPUTE_UNITS` and
`CL_DEVICE_MAX_CLOCK_FREQUENCY`.

The type and the memory total come from a single nested read: `CL_DEVICE_HOST_UNIFIED_MEMORY`
decides integrated versus discrete, and only if that query succeeds is
`CL_DEVICE_GLOBAL_MEM_SIZE` read and routed to `shared.total` for integrated devices or
`dedicated.total` for discrete ones. If the unified-memory query fails, the device keeps
`FF_GPU_TYPE_UNKNOWN` and no memory total at all.

Failure of `clGetPlatformIDs` is mapped to a specific message for `CL_PLATFORM_NOT_FOUND_KHR`,
`CL_INVALID_VALUE` and `CL_OUT_OF_HOST_MEMORY`, and to `unknown error` otherwise. Note that
the `CL_PLATFORM_NOT_FOUND_KHR` case is compiled out on macOS, where that constant does not
exist — so a Mac with no OpenCL platform reports `unknown error` instead.
