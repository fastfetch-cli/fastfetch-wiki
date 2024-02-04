Fastfetch dynamically loads needed libraries if they are available. On Linux, its only hard dependencies are `libc` (any implementation of the c standard library), `libdl`, `libm` and [`libpthread`](https://man7.org/linux/man-pages/man7/pthreads.7.html) (if built with multithreading support). They are all shipped with [`glibc`](https://www.gnu.org/software/libc/), which is already installed on most Linux distributions.

The following libraries are used if present at runtime:

### Linux and FreeBSD

* [`libvulkan`](https://www.vulkan.org/): Vulkan module & fallback for GPU output.
* [`libxcb-randr`](https://xcb.freedesktop.org/),
    [`libXrandr`](https://gitlab.freedesktop.org/xorg/lib/libxrandr),
    [`libxcb`](https://xcb.freedesktop.org/),
    [`libX11`](https://gitlab.freedesktop.org/xorg/lib/libx11): At least one of them sould be present in X11 sessions for better display detection and faster WM detection. The `*randr` ones provide multi monitor support The `libxcb*` ones usually have better performance.
* [`libwayland-client`](https://wayland.freedesktop.org/): Better display performance and output in wayland sessions. Supports different refresh rates per monitor.
* [`libdrm`](https://gitlab.freedesktop.org/mesa/drm): fall back if both wayland and x11 are not available.
* [`libGIO`](https://developer.gnome.org/gio/unstable/): Needed for values that are only stored GSettings.
* [`libDConf`](https://developer.gnome.org/dconf/unstable/): Needed for values that are only stored in DConf + Fallback for GSettings.
* [`libmagickcore` (ImageMagick)](https://www.imagemagick.org/): Images in terminal using sixel or kitty graphics protocol.
* [`libchafa`](https://github.com/hpjansson/chafa): Image output as ascii art.
* [`libZ`](https://www.zlib.net/): Faster image output when using kitty graphics protocol.
* [`libDBus`](https://www.freedesktop.org/wiki/Software/dbus): Bluetooth, Player & Media detection.
* [`libEGL`](https://www.khronos.org/registry/EGL/),
    [`libGLX`](https://dri.freedesktop.org/wiki/GLX/),
    [`libOSMesa`](https://docs.mesa3d.org/osmesa.html): At least one of them is needed by the OpenGL module for gl context creation.
* [`libOpenCL`](https://www.khronos.org/opencl/): OpenCL module
* [`libXFConf`](https://gitlab.xfce.org/xfce/xfconf): Needed for XFWM theme and XFCE Terminal font.
* [`libsqlite3`](https://www.sqlite.org/index.html): Needed for pkg & rpm package count.

### Linux only
* [`librpm`](http://rpm.org/): Slower fallback for rpm package count. Needed on openSUSE.
* [`libnm`](https://networkmanager.dev/docs/libnm/latest/): Used for Wifi detection.
* [`libpulse`](https://freedesktop.org/software/pulseaudio/doxygen/): Used for Sound device detection.
* [`libddcutil`](https://github.com/rockowitz/ddcutil): Used for brightness detection of external displays
* [`DirectX-Headers`](https://github.com/microsoft/DirectX-Headers): Used for GPU detection in WSL

### FreeBSD only
* [`v4l_compat`](https://github.com/freebsd/freebsd-ports/tree/main/multimedia/libv4l): Used for camera devices detection. See the [official document](https://docs.freebsd.org/en/books/handbook/multimedia/#webcam-setup) for detail

### macOS
<!-- All exported symbols can be found in `/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/System/Library/PrivateFrameworks/*.framework/*.tbd` -->
* [`MediaRemote`](https://iphonedev.wiki/index.php/MediaRemote.framework): Need for Media detection. It's a private framework provided by newer macOS system.
* [`DisplayServices`](https://developer.apple.com/forums/thread/666383#663154022): Need for screen brightness detection. It's a private framework provided by newer macOS system.
* [`Apple80211`](https://code.google.com/archive/p/iphone-wireless/wikis): Need for Wifi detection on Sonoma (and maybe later). It's a private framework provided by newer macOS system.
* [`MoltenVK`](https://github.com/KhronosGroup/MoltenVK): Vulkan driver for macOS. [`molten-vk`](https://github.com/Homebrew/homebrew-core/blob/HEAD/Formula/molten-vk.rb)
* [`libmagickcore` (ImageMagick)](https://www.imagemagick.org/): Images in terminal using sixel graphics protocol. [`imagemagick`](https://github.com/Homebrew/homebrew-core/blob/HEAD/Formula/imagemagick.rb)
* [`libchafa`](https://github.com/hpjansson/chafa): Image output as ascii art. [`chafa`](https://github.com/Homebrew/homebrew-core/blob/HEAD/Formula/chafa.rb)
* [`libsqlite3`](https://www.sqlite.org/index.html): Used for fast wallpaper detection before macOS Sonoma ( fallback to AppleScript )

For the image logo, iTerm with iterm image protocol should work. Apple Terminal is not supported.

### Windows

* [`wlanapi`](https://learn.microsoft.com/en-us/windows/win32/api/wlanapi/): A system dll which isn't supported by Windows Server by default. Used for Wifi info detection.
* [`libvulkan`](https://www.vulkan.org/): Vulkan module. Usually has been provided by GPU drivers. [`vulkan-loader`](https://github.com/msys2/MINGW-packages/tree/master/mingw-w64-vulkan-loader) [`vulkan-headers`](https://github.com/msys2/MINGW-packages/tree/master/mingw-w64-vulkan-headers)
* [`libOpenCL`](https://www.khronos.org/opencl/): OpenCL module. [`opencl-icd`](https://github.com/msys2/MINGW-packages/tree/master/mingw-w64-opencl-icd)

Note: In Windows 7, 8 and 8.1, [ConEmu](https://conemu.github.io/en/AnsiEscapeCodes.html) is required to run fastfetch due to [the lack of ASCII escape code native support](https://en.wikipedia.org/wiki/ANSI_escape_code#DOS,_OS/2,_and_Windows). In addition, as fastfetch for Windows targets [UCRT](https://learn.microsoft.com/en-us/cpp/windows/universal-crt-deployment) C runtime library, [it must be installed manually](https://support.microsoft.com/en-us/topic/update-for-universal-c-runtime-in-windows-c0514201-7fe6-95a3-b0a5-287930f3560c) as UCRT is only pre-installed in Windows 10 and later.

For the image logo, WezTerm with iterm image protocol is known to work, surprisingly.

### Android

* [`freetype`](https://www.freetype.org/): Used for Termux font detection. [`freetype`](https://github.com/termux/termux-packages/tree/master/packages/freetype)
* [`libvulkan`](https://www.vulkan.org/): Vulkan module, also used for GPU detection. Usually has been provided by Android system. [`vulkan-loader-android`](https://github.com/termux/termux-packages/tree/master/packages/vulkan-loader-android) [`vulkan-headers`](https://github.com/termux/termux-packages/tree/master/packages/vulkan-headers)
* [`termux-api`](https://github.com/termux/termux-api-package): Used for Wifi / Battery detection. Both the package [termux-api](https://github.com/termux/termux-packages/tree/master/packages/termux-api) and the Android app [Termux API](https://github.com/termux/termux-api) must be installed and the app `Termux API` must be configured to be able to self-running. In order to make Wifi detection actuall work, location privilege must be granted to the app `Termux API`. Read [the official doc](https://wiki.termux.com/wiki/Termux:API) for detail.
* [`libandroid-wordexp-static`](https://github.com/termux/termux-packages/tree/master/packages/libandroid-wordexp): `wordexp.h` support for Android.

For the image logo, [Termux Monet](https://github.com/HardcodedCat/termux-monet) supports iterm image protocol.
