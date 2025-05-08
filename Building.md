fastfetch uses [`cmake`](https://cmake.org/) for building. [`pkg-config`](https://www.freedesktop.org/wiki/Software/pkg-config/) is recommended for better library detection. The simplest steps to build the fastfetch and flashfetch binaries are:

```bash
mkdir -p build
cd build
cmake ..
cmake --build . --target fastfetch
```

If the build process fails to find the headers for a library listed in [dependencies](Dependencies), fastfetch will simply build without support for that specific feature. This means, it won't look for it at runtime and just act like it isn't available.

### Building on Windows

Currently GCC or clang is required (MSVC is not supported). MSYS2 with CLANG64 subsystem (or CLANGARM64 if needed) is suggested (and tested) to build fastfetch.

1. Install [MSYS2](https://www.msys2.org/#installation)
1. Open `MSYS2 / CLANG64` (not `MSYS2 / MSYS`, which targets cygwin C runtime)
1. Install dependencies

```bash
pacman -Syu mingw-w64-clang-x86_64-cmake mingw-w64-clang-x86_64-pkgconf mingw-w64-clang-x86_64-clang mingw-w64-clang-x86_64-vulkan-loader mingw-w64-clang-x86_64-vulkan-headers mingw-w64-clang-x86_64-opencl-icd mingw-w64-clang-x86_64-cppwinrt
```

Follow the building instructions of Linux next.