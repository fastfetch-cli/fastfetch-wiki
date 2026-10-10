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

Wayland, XCB and XRandR are tried first, exactly as on Linux, which is what covers Termux with an X server. When none of them reports a display, the Android display service is queried directly:

**binder** — `getDisplayIds()` followed by one `getDisplayInfo()` per id, sent to the `display` service (`android.hardware.display.IDisplayManager`) over `/dev/binder`. It needs no permission and starts no child process, which is what makes it the only route an app UID can use at all: `cmd display get-displays` does not exist before Android 13, and `dumpsys display` is behind `android.permission.DUMP`. `platformApi` reports `binder`.

It is also the only route there is. The `cmd`, `dumpsys` and vendor `getprop` routes that used to sit behind it have been removed, because binder answers on every release they covered and on the ones none of them could.

What that route has to earn is the parse. The reply is a Java `Parcelable` whose field set moves with every release and every vendor fork, so the record is never read at a fixed offset: the write order is recovered from the device's own `/system/framework/framework.jar`, from the `iget` order of `DisplayInfo.writeToParcel`, and the walk follows it field by field, anchoring every nested `Parcelable` on its class name. A field the running release does not write is simply not in the sequence, which is what makes every release's layout readable without a version check. A layout the parser does not recognise — a jar it cannot read, or a field whose framing it does not know — fails the route, and with nothing behind it the module reports no display rather than a number read out of the middle of another field.

Timed on one device, the module took 16.0–26.5 ms through `cmd display get-displays` and 2.8–3.2 ms through binder. Treat that as an order of magnitude rather than a benchmark — the absolute number moves a lot with what else the device is doing. What binder buys is the fork; most of what is left is reading the jar.

## Haiku

Displays are read from the Haiku screen API, which is what `platformApi` reports as `BScreen`.
