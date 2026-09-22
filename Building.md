fastfetch uses [CMake](https://cmake.org/) for building. [`pkg-config`](https://www.freedesktop.org/wiki/Software/pkg-config/) is recommended for better library detection. The simplest steps to build the `fastfetch` and `flashfetch` binaries are:

```bash
mkdir -p build
cd build
cmake ..
cmake --build . --target fastfetch
```

`cmake --build .` without `--target` builds everything, including `flashfetch` (see `BUILD_FLASHFETCH` below).

If the build process fails to find the headers for a library listed in [dependencies](Dependencies), fastfetch will simply build without support for that specific feature. This means it won't look for it at runtime and will act like it isn't available. Run `fastfetch --list-features` after building to see what was actually compiled in.

## Build options

Pass options to CMake with `-D<NAME>=<VALUE>`, for example:

```bash
cmake -DENABLE_LUA=OFF -DBINARY_LINK_TYPE=static ..
```

### General

| Option | Default | Description |
| - | - | - |
| `CMAKE_BUILD_TYPE` | `RelWithDebInfo` | Standard CMake build type. Note that a `Debug` build enables internal assertions. |
| `BINARY_LINK_TYPE` | `dlopen` | How shared libraries are linked: `dlopen`, `dynamic` or `static`. With `dlopen` (the default) libraries are resolved at runtime, so the binary still starts when one of them is missing. |
| `BUILD_FLASHFETCH` | `ON` | Also build the `flashfetch` benchmark binary. |
| `BUILD_TESTS` | `OFF` | Also build the test executables, which are run with `ctest`. |
| `SET_TWEAK` | `ON` | Append the git tweak to the project version. Disabled by CI for release builds. |
| `IS_MUSL` | `OFF` | Set when building against musl libc. Used by CI. |
| `INSTALL_LICENSE` | `ON` | Install the license into `/usr/share/licenses`. |
| `ENABLE_LTO` | `ON` | Use link-time optimization in release mode, if supported. |
| `ENABLE_ASAN` | `OFF` | Build with AddressSanitizer. |
| `ENABLE_TRACER` | `OFF` | Build with function tracing. |
| `ENABLE_SYSTEM_YYJSON` | `OFF` | Use a system-provided `yyjson` instead of the embedded copy. |

### Feature toggles

Most optional libraries listed in [dependencies](Dependencies) have a matching `ENABLE_<NAME>` toggle — for example `ENABLE_VULKAN`, `ENABLE_OPENCL`, `ENABLE_DBUS`, `ENABLE_SQLITE3`, `ENABLE_RPM`, `ENABLE_DRM`, `ENABLE_PULSE`. They are `ON` by default and are automatically forced to `OFF` on platforms where they don't apply.

The following ones are worth calling out:

| Option | Default | Description |
| - | - | - |
| `ENABLE_IMAGE_LOGO` | `ON` | Image logos (`sixel` / `kitty` / `iTerm` / `chafa`). Disabling it removes the whole image-logo subsystem. |
| `ENABLE_IMAGEMAGICK7` | `ON` | ImageMagick 7 decoder (Linux, BSD, SunOS, Haiku, GNU). |
| `ENABLE_IMAGEMAGICK6` | `ON` | ImageMagick 6 decoder. Deprecated — prefer ImageMagick 7. |
| `ENABLE_SIXEL` | `ON` | Embedded libsixel encoder (Windows, macOS, Android). Replaces ImageMagick's SIXEL coder on those platforms. |
| `ENABLE_CHAFA` | `ON` | The `chafa` image protocol. |
| `ENABLE_LUA` | `ON` | Lua scripting in format strings. |
| `ENABLE_QUICKJS` | `ON` | QuickJS scripting in format strings. |
| `ENABLE_LIBZFS` | `ON` | `libzfs`. |
| `ENABLE_WORDEXP` | `ON` | Use `wordexp(3)` instead of `glob(3)` for config paths, if available. |
| `ENABLE_WCWIDTH` | `ON` | Measure wide-width characters with the embedded `wcwidth` implementation. Disabling it gives a smaller and slightly faster build, but logos containing CJK or emoji characters may be measured too narrow and render incorrectly. Such use cases are uncommon. |
| `ENABLE_EMBEDDED_PCIIDS` | `OFF` | Embed `pci.ids` into the binary. Requires `python`. |
| `ENABLE_EMBEDDED_AMDGPUIDS` | `OFF` | Embed `amdgpu.ids` into the binary. Requires `python`. |

Platform-specific options:

| Option | Platform | Default | Description |
| - | - | - | - |
| `ENABLE_WIN81_COMPAT` | Windows | `ON` | Target Windows 8.1 and later. Windows 7 is not supported. |
| `ENABLE_APPLE_MEMSIZE_USABLE` | macOS | `OFF` | Report the usable memory size as the total memory size in the `Memory` module, to match other systems. |
| `CUSTOM_PCI_IDS_PATH` | Linux | empty | Path to `pci.ids`, defaults to `/usr/share/hwdata/pci.ids`. |
| `CUSTOM_AMDGPU_IDS_PATH` | Linux | empty | Path to `amdgpu.ids`, defaults to `/usr/share/libdrm/amdgpu.ids`. |
| `CUSTOM_OS_RELEASE_PATH` | Linux | empty | Path to `os-release`, defaults to `/etc/os-release`. |

### Modules and package managers

| Option | Default | Description |
| - | - | - |
| `MODULE_DISABLE_<NAME>` | `OFF` | Exclude a module from the build. `<NAME>` is the module directory name in upper case, with no separator added — for example `MODULE_DISABLE_CPU` or `MODULE_DISABLE_BLUETOOTHRADIO`. There is one such option per module. |
| `PACKAGES_DISABLE_<NAME>` | `OFF` | Skip a package manager by default. `<NAME>` is the manager name in upper case, for example `PACKAGES_DISABLE_NIX`. There is one such option per supported manager. |
| `PACKAGES_REMOVE_DISABLED` | `OFF` | Remove disabled package managers from the build entirely, instead of only skipping them at runtime. |

## Building on Windows

Currently GCC or clang is required (MSVC is not supported). MSYS2 with the CLANG64 subsystem (or CLANGARM64 if needed) is suggested (and tested) to build fastfetch.

1. Install [MSYS2](https://www.msys2.org/#installation)
1. Open `MSYS2 / CLANG64` (not `MSYS2 / MSYS`, which targets the cygwin C runtime)
1. Install dependencies

```bash
pacman -Syu mingw-w64-clang-x86_64-cmake mingw-w64-clang-x86_64-pkgconf mingw-w64-clang-x86_64-clang mingw-w64-clang-x86_64-vulkan-loader mingw-w64-clang-x86_64-vulkan-headers mingw-w64-clang-x86_64-opencl-icd mingw-w64-clang-x86_64-cppwinrt
```

Optional libraries (for example `mingw-w64-clang-x86_64-directx-headers`, which improves GPU type detection) are listed in [dependencies](Dependencies) and are not required for a working build.

Follow the building instructions for Linux next.
