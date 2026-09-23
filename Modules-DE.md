# DE

> Print desktop environment name

| | |
|---|---|
| Module type | `de` |
| Default order | 21 (only used by `--gen-config`) |
| Module source | `src/modules/de/de.c` |
| Detection source | `src/detection/displayserver/` (the name), `src/detection/de/de_linux.c` (the version) |

Prints the desktop environment that owns the current session, with its version when one could
be determined.

```
Desktop Environment: KDE Plasma 5.27.5
Desktop Environment: GNOME 44.1
Desktop Environment: Xfce4 4.18
Desktop Environment: COSMIC 0.1.0
```

## Platform support

The module exists everywhere, but the two halves have different reach: the *name* comes from the
display-server layer, and the *version* comes from `src/detection/de/`.

| Platform | DE name | DE version |
|---|---|---|
| Linux | `displayserver_linux.c` | `de_linux.c` |
| Android | `displayserver_android.c` | `de_linux.c` |
| FreeBSD / NetBSD / OpenBSD | `displayserver_linux.c` | `de_linux.c` |
| Solaris / illumos | `displayserver_linux.c` | `de_linux.c` |
| GNU/Hurd | `displayserver_linux.c` | `de_linux.c` |
| Haiku | `displayserver_haiku.cpp` | `de_nosupport.c` |
| macOS | `displayserver_apple.c` | `de_nosupport.c` |
| Windows | `displayserver_windows.c` | `de_nosupport.c` |

`de_nosupport.c` is registered in the Haiku, macOS and Windows blocks, so the module always
reports `No DE found` on those three — the display-server layer there knows a window manager
but never a desktop environment. This is the mirror image of the `WM` module, which is
unsupported on Solaris and Haiku instead.

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

There are no module-specific keys. Version detection is controlled by the global
`general.detectVersion` setting.

`slowVersionDetection` is accepted but rejected: passing it prints
`Key slowVersionDetection is deprecated, it's always true` and the module continues. The key
dates from when version detection was opt-in; it is now unconditional.

## Format string

Run `fastfetch -h de-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{process-name}` | The session process, e.g. `plasmashell` or `gnome-shell` |
| `{pretty-name}` | The display name, e.g. `KDE Plasma` |
| `{version}` | Version string, when one could be determined |

`{pretty-name}` is not always a recognised desktop environment: when nothing matches, the
value of `XDG_CURRENT_DESKTOP` (or one of its four fallbacks) is used verbatim, so
`{pretty-name}` can be `COSMIC`, `TDE` or any custom string.

## JSON output

```jsonc
{
    "type": "DE",
    "result": {
        "processName": "plasmashell",
        "prettyName": "KDE Plasma",
        "version": "5.27.5"
    }
}
```

On failure the object is `{ "type": "DE", "error": "No DE found" }`.

## Examples

```jsonc
{ "type": "de", "key": "DE", "format": "{pretty-name} {version}" }
```

```jsonc
{ "type": "de", "format": "{process-name}" }
```

## Pitfalls

- **Only three platforms can find a DE at all.** macOS, Windows and Haiku always print
  `No DE found`, because no desktop environment is detectable there — only a window manager.
  A format string that assumes `{pretty-name}` is non-empty will render blank on those.
- **Four recognised names never get a version.** Measured against the real dispatch table,
  `CDE`, `UKUI`, `LXDE` and `NebiDE` all return `Unsupported DE` and leave `{version}` empty.
  `CDE` and `UKUI` are produced by the display-server layer's pretty-name table; `LXDE` and
  `NebiDE` have constants defined for them but no mapping, so they only appear when
  `XDG_CURRENT_DESKTOP` happens to spell them that way. `GNOME Classic` used to be on this list
  and is not any more — it is `GNOME` with `GNOME_SHELL_SESSION_MODE=classic`, so the version
  branch now routes it to the same `gnome-shell` probe as plain `GNOME`.
- **Trinity is matched case-insensitively and accepts two spellings.** The display-server layer
  produces `Trinity` from `TDE_FULL_SESSION` and `TDE` from `XDG_CURRENT_DESKTOP`, so the dispatch
  compares against both, ignoring case. Measured: `Trinity`, `trinity` and `TDE` all reach the
  version probe; only the raw `tde-config` output decides whether `{version}` ends up non-empty.
  Note that `TDE` is the *display-server* spelling, while `{pretty-name}` may still render
  something else.
- **`{version}` is empty for anything not on the list.** The version probe is a fixed
  `if`/`else if` chain over twelve names; anything else — including every custom
  `XDG_CURRENT_DESKTOP` value — silently reports no version and no error.
- **Version detection runs external commands.** For KDE, GNOME (and GNOME Classic), Cinnamon,
  Xfce4, Mate, LXQt, COSMIC and Enlightenment the probe falls back to running the session binary
  with `--version` (and Trinity with `tde-config --version`) when the file-based probes come up
  empty. `xfce4-session --version` and `lxqt-session -v` are noted in the source as
  particularly slow. Set `general.detectVersion` to `false` to keep fastfetch side-effect free.
- **The version can come from a shared library.** The Xfce4 probe prefers
  `dlopen("libxfce4util.so.7")` and calls `xfce_version_string()`; a build with `FF_DISABLE_DLOPEN`
  skips straight to spawning the binary.
- **Some versions are read out of `$HOME` or the environment.** Cinnamon reads
  `$CINNAMON_VERSION` first, COSMIC reads `$COSMIC_VERSION` first, and the file-based probes
  search `XDG_DATA_DIRS` (through `ffParsePropFileData`) rather than fixed paths — so the value
  reflects the user's environment, not the system package database.
- **`{process-name}` is a hard-coded guess for most DEs.** It is not the process that was
  actually found: the display-server layer sets it to a fixed string such as `plasmashell`,
  `xfce4-session` or `enlightenment_start` whenever the *display name* matches. For LXQt it is
  always `lxqt-session` even though the session may run a different window manager.
- **The DE name can come from a window-manager name.** If nothing is recognised, the last
  fallback assigns `XDG_CURRENT_DESKTOP` to whichever of the DE and WM slots is still empty, so
  a compositor that sets `XDG_CURRENT_DESKTOP` to its own name shows up as the *desktop
  environment*. That is how `COSMIC` is reported.
- **`{pretty-name}` is the key for everything downstream.** `wmtheme` and `gtk` compare
  `dePrettyName` against the same `GNOME Classic` / `NebiDE` strings to decide which theme
  backend to use, so a custom `XDG_CURRENT_DESKTOP` value silently disables those too.

## Implementation

The module itself does no detection: `ffPrintDE()` asks the display-server layer for the
cached `FFDisplayServerResult`, errors out when `dePrettyName` is empty, and then calls
`ffDetectDEVersion()` to fill `{version}`.

### Where the name comes from

`ffConnectDisplayServer()` returns a cached `FFDisplayServerResult` — the cache is invalidated
at each `--dynamic-interval` round boundary, so the detection below runs once per round, not
once per process. It populates both `deProcessName` and `dePrettyName`. On Linux, Android, BSD
and Solaris the work happens in `src/detection/displayserver/linux/wmde.c`:

1. `parseEnv()` reads `XDG_CURRENT_DESKTOP`, `XDG_SESSION_DESKTOP`, `CURRENT_DESKTOP`,
   `SESSION_DESKTOP` and `DESKTOP_SESSION` in that order, then falls back to probing
   `KDE_FULL_SESSION` / `KDE_SESSION_UID` / `KDE_SESSION_VERSION`, `GNOME_DESKTOP_SESSION_ID`,
   `MATE_DESKTOP_SESSION_ID`, `TDE_FULL_SESSION`, `HYPRLAND_CMD`, `SWAYSOCK` and
   `COSMIC_VERSION`, plus a `/mnt/wslg/` check on Linux.
2. `applyPrettyNameIfDE()` matches that value, case-insensitively, against a fixed list and
   sets both fields. KDE additionally consults `$KDEWM`, and LXQt reads `window_manager` from
   `lxqt/session.conf`, so that the window manager can be resolved before the process scan.
3. If either field is still empty, `getFromProcesses()` walks every process owned by the
   current user — `sysctl({CTL_KERN, KERN_PROC, KERN_PROC_UID})` on FreeBSD and NetBSD,
   `KERN_PROC2` on NetBSD, `/proc` with `loginuid` filtering on Solaris, and a `/proc` walk on
   Linux that prefers `/proc/<pid>/exe` with `basename()` and falls back to
   `/proc/<pid>/comm` — applying the same two matchers to each process name. The scan stops as
   soon as both fields are set.
4. Whatever is still empty is filled from the environment value, which is how an unrecognised
   desktop gets reported.

The connection to the display server itself only ever yields a window manager, which is why
step 3 exists at all; the module relies on it for the DE half.

### Where the version comes from

`ffDetectDEVersion()` is a single `if`/`else if` chain over the pretty name, gated on
`general.detectVersion`. Each branch is a short list of file probes followed by a process
invocation:

- **KDE Plasma** — reads `X-KDE-PluginInfo-Version` from `plasma.desktop`, `plasmax11.desktop`,
  `plasma5.desktop`, `plasmawayland.desktop` or `plasmawayland5.desktop`, under
  `<pkg base>/share/wayland-sessions/` and `<pkg base>/share/xsessions/` first and then through
  `XDG_DATA_DIRS`; failing that it runs `plasmashell --version` and keeps the last
  space-separated token. `<pkg base>` is `FF_PATH_PKG_BASE` — `/usr` normally, `/usr/local` on
  FreeBSD and OpenBSD, `/usr/pkg` on NetBSD.
- **GNOME and GNOME Classic** — asks the session bus for `org.gnome.Shell.ShellVersion` on
  `/org/gnome/Shell`, then runs `gnome-shell --version` and takes the last token. The D-Bus
  path is compiled out when `FF_HAVE_DBUS` is not defined. GNOME Classic is the same shell in
  classic mode, so it takes this identical path.
- **Cinnamon** — `$CINNAMON_VERSION`, then `X-GNOME-Bugzilla-Version` from
  `applications/cinnamon.desktop`, then `cinnamon --version`.
- **Xfce4** — `xfce_version_string()` from `libxfce4util`, then `xfce4-session --version` with
  the text before the first `)` and after the last space kept.
- **Mate** — reads `<platform>`, `<minor>` and `<micro>` out of `mate-about/mate-version.xml`
  and joins them with `ffParseSemver()`, then falls back to `mate-session --version`.
- **LXQt** — three file probes (`gconfig/lxqt.pc`, `cmake/lxqt/lxqt-config.cmake`,
  `cmake/lxqt/lxqt-config-version.cmake`), then `lxqt-session -v` with the `liblxqt` line
  extracted from its output.
- **Budgie** — a single read of `budgie/budgie-version.xml`, looking for `<str>`.
- **Unity** — reads the `version= "%prog ` option out of the `/usr/bin/unity` script and cuts
  at the first quote.
- **COSMIC** — `$COSMIC_VERSION`, then `cosmic-comp --version` with the second field kept.
- **Enlightenment** — calls the `Version` method on
  `org.enlightenment.wm.Core` over the session bus, then parses `Version: ` out of
  `enlightenment --version`.
- **Trinity / TDE** — locates `tde-config`, derives `../lib/libtdecore.so` from its path and scans
  that binary for a string matching `R<major>.<minor>.<patch>`, then falls back to
  `tde-config --version` and looks for a `TDE: ` line. The dispatch reaches this branch for both
  `Trinity` and `TDE`, ignoring case.

Everything else returns `Unsupported DE` and leaves the buffer untouched.
