Fastfetch dynamically loads needed libraries if they are available. On Linux, its only hard dependencies are `libc` (any implementation of the c standard library), `libdl`, `libm` and [`libpthread`](https://man7.org/linux/man-pages/man7/pthreads.7.html) (if built with multithreading support). They are all shipped with [`glibc`](https://www.gnu.org/software/libc/), which is already installed on most Linux distributions.

The following libraries are used if present at runtime. Which of them were found when your binary was built is reported by `fastfetch --list-features`.

### All platforms

* [`libZ`](https://www.zlib.net/): Zlib compression when sending HTTP requests. Faster image output when using kitty graphics protocol.
* [`libzfs`](https://github.com/openzfs/zfs): Zpool module.
* [Lua](https://www.lua.org/) 5.3 to 5.5, or [QuickJS](https://github.com/quickjs-ng/quickjs) v0.15.0 or newer: Lua and JavaScript scripting in format strings.

### Linux and FreeBSD

* [`libvulkan`](https://www.vulkan.org/): Vulkan module & fallback for GPU output.
* [`libxcb-randr`](https://xcb.freedesktop.org/),
    [`libXrandr`](https://gitlab.freedesktop.org/xorg/lib/libxrandr): At least one of them should be present in X11 sessions for better display detection and faster WM detection.
* [`libwayland-client`](https://wayland.freedesktop.org/): Better display performance and output in wayland sessions. Supports different refresh rates per monitor.
* [`libdrm`](https://gitlab.freedesktop.org/mesa/drm): fall back if both wayland and x11 are not available. AMD GPU properties detection
* [`libGIO`](https://developer.gnome.org/gio/unstable/): Needed for values that are only stored in GSettings.
* [`libDConf`](https://developer.gnome.org/dconf/unstable/): Needed for values that are only stored in DConf + Fallback for GSettings.
* [`libeet`](https://www.enlightenment.org/): Enlightenment window manager configuration detection
* [`libmagickcore` (ImageMagick 7)](https://www.imagemagick.org/): Images in terminal using sixel or kitty graphics protocol. ImageMagick 6 works too but is deprecated, and is only kept for old Debian and Ubuntu releases that have no ImageMagick 7.
* [`libchafa`](https://github.com/hpjansson/chafa): Image output as ascii art.
* [`libDBus`](https://www.freedesktop.org/wiki/Software/dbus): Bluetooth, Player & Media, XFCE theme detection.
* [`libEGL`](https://www.khronos.org/registry/EGL/),
    [`libGLX`](https://dri.freedesktop.org/wiki/GLX/): At least one of them is needed by the OpenGL module for gl context creation (EGL preferred).
* [`libOpenCL`](https://www.khronos.org/opencl/): OpenCL module
* [`libsqlite3`](https://www.sqlite.org/index.html): Needed for the rpm, moss and soar package counts, and for values that are only stored in a SQLite database, such as GSettings.
* [`libelf`](https://sourceware.org/elfutils/): Much faster version detection for many things. Necessary for st terminal font detection.
* [`libva-drm`](https://github.com/intel/libva),
    [`libva-x11`](https://github.com/intel/libva): Hardware video acceleration codec detection via VA-API.
* [`libvdpau`](https://http.download.nvidia.com/XFree86/vdpau/doxygen/html/index.html): Hardware video acceleration codec detection via VDPAU-API.

### Linux only
* linux-headers (required)
* [`librpm`](http://rpm.org/): Slower fallback for rpm package count. Needed on openSUSE.
* [`libpulse`](https://freedesktop.org/software/pulseaudio/doxygen/): Used for Sound device detection.
* [`libddcutil`](https://github.com/rockowitz/ddcutil): Used for brightness detection of external displays.

### FreeBSD only
* [`v4l_compat`](https://github.com/freebsd/freebsd-ports/tree/main/multimedia/libv4l): Used for camera devices detection. See the [official document](https://docs.freebsd.org/en/books/handbook/multimedia/#webcam-setup) for details.

### macOS
<!-- All exported symbols can be found in `/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/System/Library/PrivateFrameworks/*.framework/*.tbd` -->
* [`MediaRemote`](https://iphonedev.wiki/index.php/MediaRemote.framework): Needed for Media detection. It's a private framework provided by newer macOS systems.
* [`DisplayServices`](https://developer.apple.com/forums/thread/666383#663154022): Needed for screen brightness detection. It's a private framework provided by newer macOS systems.
* [`MoltenVK`](https://github.com/KhronosGroup/MoltenVK): Vulkan driver for macOS. [`molten-vk`](https://github.com/Homebrew/homebrew-core/blob/HEAD/Formula/molten-vk.rb)
* [`libchafa`](https://github.com/hpjansson/chafa): Image output as ascii art. [`chafa`](https://github.com/Homebrew/homebrew-core/blob/HEAD/Formula/chafa.rb)
* [`libsqlite3`](https://www.sqlite.org/index.html): Used for fast wallpaper detection before macOS Sonoma (fallback to AppleScript).

Image logos are decoded by the `ImageIO` framework, which is part of macOS, so ImageMagick is not needed. Sixel output is produced by an embedded encoder. Any terminal that supports sixel works; iTerm2 works with the iterm image protocol, and kitty, Ghostty and WezTerm with the kitty graphics protocol. Apple Terminal supports neither.

### Windows

* [`libvulkan`](https://www.vulkan.org/): Vulkan module. Usually has been provided by GPU drivers. [`vulkan-loader`](https://github.com/msys2/MINGW-packages/tree/master/mingw-w64-vulkan-loader) [`vulkan-headers`](https://github.com/msys2/MINGW-packages/tree/master/mingw-w64-vulkan-headers)
* [`libOpenCL`](https://www.khronos.org/opencl/): OpenCL module. [`opencl-icd`](https://github.com/msys2/MINGW-packages/tree/master/mingw-w64-opencl-icd)
* [`cppwinrt`](https://github.com/microsoft/cppwinrt): C++/WinRT headers for MinGW. Used for media detection. [`cppwinrt`](https://github.com/msys2/MINGW-packages/tree/master/mingw-w64-cppwinrt) (Build only)
* [`directx-headers`](https://github.com/microsoft/DirectX-Headers): More accurate GPU type detection for certain devices. Usually not necessary. [`directx-headers`](https://github.com/msys2/MINGW-packages/tree/master/mingw-w64-directx-headers)

Note: In Windows 7, 8 and 8.1, [ConEmu](https://conemu.github.io/en/AnsiEscapeCodes.html) is required to run fastfetch due to [the lack of ASCII escape code native support](https://en.wikipedia.org/wiki/ANSI_escape_code#DOS,_OS/2,_and_Windows). In addition, as fastfetch for Windows targets [UCRT](https://learn.microsoft.com/en-us/cpp/windows/universal-crt-deployment) C runtime library, [it must be installed manually](https://support.microsoft.com/en-us/topic/update-for-universal-c-runtime-in-windows-c0514201-7fe6-95a3-b0a5-287930f3560c) as UCRT is only pre-installed in Windows 10 and later.

Image logos are decoded by `WIC`, which is part of Windows, so ImageMagick is not needed. Sixel output is produced by an embedded encoder, which makes `fastfetch --sixel X:\path\to\image` work out of the box on Windows Terminal. WezTerm is known to work with the iterm image protocol, surprisingly.

### Android

* [`freetype`](https://www.freetype.org/): Used for Termux font detection. [`freetype`](https://github.com/termux/termux-packages/tree/master/packages/freetype)
* [`libvulkan`](https://www.vulkan.org/): Vulkan module, also used for GPU detection. Usually has been provided by the Android system. [`vulkan-headers`](https://github.com/termux/termux-packages/tree/master/packages/vulkan-headers) [`vulkan-loader-android`](https://github.com/termux/termux-packages/tree/master/packages/vulkan-loader-android)
* [`libOpenCL`](https://www.khronos.org/opencl/): OpenCL module. Usually has been provided by the Android system. [`ocl-icd`](https://github.com/termux/termux-packages/tree/master/packages/ocl-icd) [`opencl-headers`](https://github.com/termux/termux-packages/tree/master/packages/opencl-headers)
    * You usually need to set `LD_LIBRARY_PATH` to let opencl loader find the drivers. `LD_LIBRARY_PATH=/vendor/lib64 fastfetch`
* [`libEGL`](https://www.khronos.org/registry/EGL/): OpenGL module. Installing EGL headers is enough. [mesa-dev](https://github.com/termux/termux-packages/blob/master/packages/mesa/mesa-dev.subpackage.sh)
* [Termux:API](https://github.com/termux/termux-api): The Android app is needed for Wifi detection, because it is what carries the Wi-Fi permission that Termux itself does not request. Uninstalling it takes the permission off the shared UID and the module stops reporting the connection. The `termux-api` command package is **not** needed: Wifi, Battery, Camera and Display detection all read from the Android services directly now.
* [`libandroid-wordexp-static`](https://github.com/termux/termux-packages/tree/master/packages/libandroid-wordexp): `wordexp.h` support for Android. (Build only)
* chafa, dbus, glib/gio, libelf, libxcb, libxrandr, pulseaudio, zlib: See the sections above.

Image logos are decoded by `AImageDecoder`, which needs Android 11 (API 30), so a build running on Android 10 or older reports an error instead. Animated logos additionally need Android 12 (API 31), and the repeat count is not available there, so an animation is reported as looping forever. [Termux Monet](https://github.com/HardcodedCat/termux-monet) supports the iterm image protocol.
