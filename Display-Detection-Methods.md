Fastfetch supports the following display detection methods. Which one answered is reported by the Display module as `platformApi`.

## Linux and FreeBSD

The methods are tried in order, and the first one that reports at least one display wins:

1. **wayland** — via `libwayland-client`. Supports the most features, including per-monitor refresh rates. It cannot detect the name of the WM / DE, which is filled in separately from environment variables and the process list.
    * Reported as `wayland-base`, `wayland-zxdg`, `wayland-wpcolor`, `wayland-zxdg+wpcolor`, or `wayland-kde` when the KDE output device protocol is available.
2. **xcb-randr** — via `libxcb-randr`.
    * Reported as `xcb-randr-mode` when the current mode was found in the mode list, and `xcb-randr-crtc` when only the CRTC size is known, in which case no refresh rate is reported. An `emu` infix (`xcb-randr-emu-mode`, `xcb-randr-emu-crtc`) means the values come from an emulated RandR, as under XWayland.
    * `xcb-randr-monitor` and `xcb-randr-screen` are the fallbacks used when the per-output data is not usable.
3. **xlib-randr** — via `libXrandr`. Reported as `xlib-randr-crtc`, `xlib-randr-mode`, `xlib-randr-monitor` or `xlib-randr-screen`, with the same `emu` infix.
4. **libdrm** — display server independent, tried when none of the above reported a display.
    * Reported as `libdrm`.
5. **sysfs-drm** — `/sys/class/drm/`, used when `libdrm` is unavailable or failed.
    * Reported as `sysfs-drm`.
6. **kenv** — FreeBSD only. Reads `screen.width` and `screen.height` from the kernel environment.
    * Reported as `kenv`.

Use `--ds-force-drm` to skip wayland and X11 and go straight to DRM:

| Value | Description |
|-------|-------------|
| `false` | The default. Tries `wayland`, then `x11`, then `drm` |
| `true` | Tries `libdrm` first, then `sysfs` if `libdrm` fails |
| `sysfs-only` | Uses `/sys/class/drm` only |

## macOS

Displays are read from the `CoreGraphics` framework, which is what `platformApi` reports. `--ds-force-drm` has no effect.

## Windows

Displays are read through `GDI`, which is what `platformApi` reports. `--ds-force-drm` has no effect.

## Android

Wayland, XCB and XRandR are tried first, exactly as on Linux, which is what covers Termux with an X server. When none of them reports a display, the Android display service is queried instead:

1. **cmd** — `/system/bin/cmd display get-displays`. Needs no permission, so it also works for an app UID. Requires Android 13 (API 33).
2. **dumpsys** — `/system/bin/dumpsys display`. The only route that answers on Android 12 and older, and only for `adb shell` and root.
3. **getprop** — a vendor property that only some devices set. The last resort.

`platformApi` reports which of the three answered: `cmd`, `dumpsys` or `getprop`.

## Haiku

Displays are read from the Haiku screen API, which is what `platformApi` reports as `BScreen`.
