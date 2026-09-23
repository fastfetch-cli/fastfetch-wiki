# OpenGL

> Print the highest OpenGL version supported by the GPU

| | |
|---|---|
| Module type | `opengl` |
| Default order | 56 (only used by `--gen-config`) |
| Module source | `src/modules/opengl/opengl.c` |
| Detection source | `src/detection/opengl/` |

Prints a single line holding the OpenGL version string:

```
OpenGL: 4.6.0 NVIDIA 616.56
```

The version is whatever `glGetString(GL_VERSION)` reports for a context that fastfetch creates for the
occasion — it is not parsed, not normalised and not truncated, so the trailing vendor-specific part
(`NVIDIA 616.56`) is part of the value. The renderer, the vendor, the shading language version and the
window-system binding used are read at the same time but are not part of the default line; they are
only reachable through a custom `format` or the JSON output.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `opengl_linux.c` | EGL (`libEGL`) first, GLX (`libGLX`, or `libGL` on NetBSD / OpenBSD) as fallback |
| Android | `opengl_linux.c` | Same source; EGL is the usual path |
| FreeBSD / MidnightBSD / DragonFly | `opengl_linux.c` | EGL and GLX both available |
| NetBSD | `opengl_linux.c` | GLX is resolved from `libGL` instead of `libGLX` |
| OpenBSD | `opengl_linux.c` | GLX is resolved from `libGL` instead of `libGLX` |
| Solaris / illumos | `opengl_linux.c` | EGL and GLX both available |
| GNU/Hurd | `opengl_linux.c` | EGL and GLX both available |
| macOS | `opengl_apple.c` | CGL, asking for a 3.2 core profile |
| Windows | `opengl_windows.c` | WGL through `opengl32.dll` |
| Haiku | `opengl_haiku.cpp` | `BGLView` from the OpenGLKit |

Every platform also compiles `opengl_shared.c`, which holds the two shared helpers
(`ffOpenGLHandleResult()` and the EGL path). Which of EGL / GLX actually exists in a given binary is a
build-time decision: `ENABLE_EGL` defaults to `ON` everywhere except macOS, and `ENABLE_GLX` defaults
to `ON` on Linux, the FreeBSD family, NetBSD, OpenBSD, Android, Solaris and GNU — but never on
Windows, macOS or Haiku. Asking for a library that was not compiled in yields
`fastfetch was compiled without egl support` (or `… glx support`), and asking for GLX on Windows,
macOS or Haiku yields `Unsupported OpenGL library`.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `library` | string | `auto` | Which window-system binding to use: `auto`, `egl` or `glx` |
| `key` | string | `OpenGL` | Module key. A single space hides the key and the separator |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

`library` is the only module-specific key. `auto` picks the platform's native binding — EGL on Linux
and friends, CGL on macOS, WGL on Windows, OpenGLKit on Haiku. On Linux and friends, when the build
has both EGL and GLX, `auto` tries **EGL first and falls back to GLX** if EGL fails for any reason.

An invalid value (`{"library": "bogus"}`) prints `OpenGL: Invalid library value: Invalid enum string`
but does **not** disable the module: `library` keeps its default, so the module then runs with `auto`.
The error appears in the text output only when `display.showErrors` is `true`, and it appears before
the module's own line.

## Format string

Run `fastfetch -h opengl-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{version}` | OpenGL version |
| `{renderer}` | OpenGL renderer |
| `{vendor}` | OpenGL vendor |
| `{slv}` | OpenGL shading language version |
| `{library}` | OpenGL library used |

None of the five is marked `*` in the help output, so none of them is available in the key format: a
`key` such as `GL[{version}]` prints `{version}` literally.

`{library}` is a free-form string produced by the backend, not an enum: `WGL 1.0` on Windows,
`EGL 1.5` (the EGL implementation's own version) on the EGL path, `GLX 1.4` (the GLX version queried
from the X server) on the GLX path, `CGL 1.2`-style major/minor on macOS, and the literal
`OpenGLKit` on Haiku.

## JSON output

```jsonc
[
    {
        "type": "OpenGL",
        "result": {
            "version": "4.6.0 NVIDIA 616.56",
            "renderer": "NVIDIA GeForce RTX 4070 Ti/PCIe/SSE2",
            "vendor": "NVIDIA Corporation",
            "slv": "4.60 NVIDIA",
            "library": "WGL 1.0"
        }
    }
]
```

- When detection fails the object is `{"type": "OpenGL", "error": "…"}` instead — there is no
  `result` key and no partially filled object.
- All five fields are strings, never numbers and never `null`.
- The key order inside `result` is `version`, `renderer`, `vendor`, `slv`, `library`.

## Examples

```jsonc
// The renderer instead of the version
{ "type": "opengl", "format": "{renderer}" }
```

```jsonc
// Everything the detection layer read
{ "type": "opengl", "format": "{version} | {slv} | {vendor} | {renderer} | {library}" }
```

```jsonc
// Force the EGL path and show which binding answered
{ "type": "opengl", "library": "egl", "format": "{version} via {library}" }
```

## Pitfalls

- **The module creates and destroys a real GL context on every run.** Nothing is cached, so a
  `--dynamic-interval` run initialises EGL (or opens an X display, or creates a window) once per
  round. On Linux this is also why the module can be slow, and why it needs a display at all: without
  an X server and without a working surfaceless EGL platform it fails.
- **The version string is not the "highest supported" number in any comparable form.** It is the
  `GL_VERSION` of whatever context the driver handed out for a 1×1 buffer, which for a core-profile
  request can differ from what a compatibility profile would report.
- **`auto` silently prefers EGL on Linux.** A machine where EGL works but GLX is the "better" path
  still reports EGL, and the only way to notice is to look at `{library}`. There is no fallback chain
  beyond the single EGL → GLX step; if EGL fails and GLX is not compiled in, the module errors out.
- **`glx` is rejected on Windows, macOS and Haiku** with `Unsupported OpenGL library`, and `egl` is
  rejected on a build without EGL headers. Both are hard errors, not fallbacks.
- **An invalid `library` value does not stop the module.** It reports the enum error and continues
  with the default, so a config typo produces a module that still works but silently ignores the
  requested binding.
- **The Windows path briefly creates a real window.** `opengl_windows.c` registers a window class
  named `ogl_version_check`, creates a 1×1 window, makes a WGL context current, reads the strings and
  tears everything down. On a headless session the window creation can fail and the module reports
  `CreateWindowW() failed`.
- **`FF_SUPPRESS_IO()` is active while the context is created.** Driver chatter that the EGL / GLX
  initialization would normally print to stdout and stderr is suppressed, which is also why a failure
  usually surfaces as a one-line message rather than a driver log.

## Implementation

`ffDetectOpenGL()` fills an `FFOpenGLResult` (five `FFstrbuf`s: `version`, `renderer`, `vendor`,
`slv`, `library`) and returns an error string. Both `ffPrintOpenGL()` and
`ffGenerateOpenGLJsonResult()` call it themselves, so a JSON run and a text run each initialise their
own context.

Reading the strings is shared: `ffOpenGLHandleResult()` in `opengl_shared.c` calls
`glGetString()` for `GL_VERSION`, `GL_RENDERER`, `GL_VENDOR` and `GL_SHADING_LANGUAGE_VERSION`.
`GL_SHADING_LANGUAGE_VERSION` is defined locally as `0x8B8C` when the header in use (notably WGL's)
does not define it.

### Linux, Android, the BSDs, Solaris, GNU

`auto` runs `ffOpenGLDetectByEGL()` and, if that returns an error and the build has GLX, retries with
`detectByGlx()`. `egl` and `glx` skip the chain and report `fastfetch was compiled without … support`
when the corresponding backend is missing.

The EGL path loads `libEGL`, resolves eleven symbols plus `glGetString` via `eglGetProcAddress`,
prefers `eglGetPlatformDisplay(EGL_PLATFORM_SURFACELESS_MESA, …)` over `eglGetDisplay()`, and then
initialises, binds `EGL_OPENGL_API` (falling back to the default API — usually OpenGL ES — when that
fails), takes the first config, creates a 1×1 pbuffer and a context. Context creation asks for
`EGL_CONTEXT_CLIENT_VERSION` 1 first and, when that fails on the GLES path, retries with no
attributes at all to accommodate ANGLE builds that only implement GLES 1.1. `library` becomes
`EGL <eglQueryString(EGL_VERSION)>`.

The GLX path loads `libGLX` (or `libGL` on NetBSD and OpenBSD), opens the display with
`XOpenDisplay(nullptr)`, picks a visual with `glXChooseVisual`, creates a 1×1 pixmap, binds it with
`glXCreateGLXPixmap`, creates a context and makes it current. `glGetString` comes from
`glXGetProcAddress`, not from the library's export table. `library` becomes `GLX <major.minor>` when
`glXQueryVersion()` succeeds and the bare `GLX` otherwise.

### Windows

`opengl32.dll` is loaded and `wglMakeCurrent`, `wglCreateContext`, `wglDeleteContext` and
`glGetString` are resolved from it. A window class `ogl_version_check` is registered with
`CS_OWNDC`, a 1×1 window is created, a pixel format with 32 colour bits and 24 depth bits is chosen
and set, and a WGL context is made current for the duration of the query. `library` is the constant
`WGL 1.0`.

### macOS

`CGLChoosePixelFormat()` is asked for `kCGLOGLPVersion_3_2_Core` with `kCGLPFAAccelerated`, a context
is created and made current, and `CGLGetVersion()` supplies the numbers for `CGL <major.minor>`. The
strings are read with the system `glGetString`.

### Haiku

A `BApplication` and a `BGLView` (`BGL_RGB`) are constructed, `glGetString` is obtained through
`BGLView::GetGLProcAddress()`, and `library` is the constant `OpenGLKit`.
