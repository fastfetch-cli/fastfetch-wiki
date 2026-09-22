# TerminalSize

> Print the current terminal size

| | |
|---|---|
| Module type | `terminalsize` |
| Default order | 31 (only used by `--gen-config`) |
| Module source | `src/modules/terminalsize/terminalsize.c` |
| Detection source | `src/detection/terminalsize/` |

Prints the terminal window size in characters, and in pixels when the terminal reports them.

```
Terminal Size: 120 columns x 30 rows
Terminal Size: 80 columns x 24 rows (640px x 384px)
```

The printed key is `Terminal Size` (the module type is `terminalsize`). The pixel part is appended
only when **both** dimensions are non-zero, so a terminal that reports only one of them prints the
character size alone.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `terminalsize_linux.c` | `ioctl(TIOCGWINSZ)`, with two escape-code fallbacks |
| Android | `terminalsize_linux.c` | Same file |
| FreeBSD / NetBSD / OpenBSD / DragonFly | `terminalsize_linux.c` | Same file |
| Solaris / illumos | `terminalsize_linux.c` | Same file; pulls in `sys/termios.h` |
| macOS | `terminalsize_linux.c` | Same file |
| Haiku | `terminalsize_linux.c` | Same file |
| GNU/Hurd | `terminalsize_linux.c` | Same file |
| Windows | `terminalsize_windows.c` | `GetConsoleScreenBufferInfo()`, with the same escape-code fallbacks |

Every platform has an implementation.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | `Terminal Size` | Module key. A single space hides the key and the separator |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

There are no module-specific options.

## Format string

Run `fastfetch -h terminalsize-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{rows}` | Terminal rows |
| `{columns}` | Terminal columns |
| `{width}` | Terminal width in pixels |
| `{height}` | Terminal height in pixels |

None of the four is marked `*` in the help output, so none is available in the key format.

`{width}` and `{height}` print `0` when the terminal does not report pixels; the default output
hides that case, a custom format does not:

```jsonc
{ "type": "terminalsize", "format": "cols={columns} rows={rows} w={width} h={height}" }
```

```
Terminal Size: cols=120 rows=30 w=0 h=480
```

## JSON output

```jsonc
[
    {
        "type": "TerminalSize",
        "result": {
            "columns": 120,
            "rows": 30,
            "width": 0,
            "height": 480
        }
    }
]
```

- All four keys are always present and are numbers — the module never emits `null`.
- On failure the object carries `error` instead and has no `result`.
- `width` / `height` are `0`, not absent, when the terminal cannot report pixels.
- The key order is `columns`, `rows`, `width`, `height` — note that `rows` comes second even though
  the format-variable list starts with `{rows}`.

## Examples

```jsonc
// Characters only, the way the default line prints it
{ "type": "terminalsize", "format": "{columns} x {rows}" }
```

```jsonc
// Screen size in pixels, for a script that wants to place a logo
{ "type": "terminalsize", "format": "{width}x{height}" }
```

## Pitfalls

- **`{width}` and `{height}` are `0` on many terminals.** Pixel size is optional in the protocol;
  xterm, most GPU terminals and every non-tty environment answer the character query but not the
  pixel one. The default line drops the whole `(…px)` part in that case, so a missing suffix means
  "unknown", not "zero".
- **The character size alone decides success.** The Unix backend fails when both `ws_row` and
  `ws_col` are `0` — even if the pixel dimensions were obtained. A terminal that answers `\e[14t`
  but not `\e[18t` therefore reports nothing at all.
- **Two escape-code queries can add up to two seconds.** When the ioctl yields no size, `\e[18t` is
  sent; when the pixel size is missing, `\e[14t` is sent. Each wait is up to
  `FF_IO_TERM_RESP_WAIT_MS` (1000 ms) for a terminal that never answers — so a redirected or
  piped run can pause noticeably.
- **On Windows the reported size is the visible window, not the screen buffer.** It comes from
  `srWindow` of `CONSOLE_SCREEN_BUFFER_INFO`, so a console scrolled back in its buffer still
  reports the visible viewport.
- **On Windows the pixel size is derived, not reported.** It is
  `columns * fontWidth` / `rows * fontHeight` from `GetCurrentConsoleFontEx()`, which only works
  for ConHost; the escape-code query is the fallback. The source notes that Windows Terminal does
  not answer these queries, so the pixel fields are commonly `0` there.
- **The size is read from the tty, not from the process environment.** On Unix stdout is used when
  it is a tty and `/dev/tty` otherwise, so `fastfetch > file` still reports the real terminal size
  rather than nothing. That fallback file descriptor is opened once per process and cached in a
  function-static.
- **The module has no `--dynamic-interval` special handling.** It is a plain syscall per call, so it
  tracks a resize without any caching layer involved.
- **Errors are invisible by default.** `Failed to detect terminal size` goes through
  `ffPrintError()` and needs `display.showErrors` to be `true` to be seen; otherwise the module
  silently disappears.

## Implementation

`ffPrintTerminalSize()` calls `ffDetectTerminalSize()`, which fills a four-field
`FFTerminalSizeResult` by value (`uint16_t` each), and prints
`"%u columns x %u rows"` plus an optional `" (%upx x %upx)"`. With a custom `format` the four
numbers are handed to the format engine directly, so `0` is printed as `0` instead of being hidden.
`ffGenerateTerminalSizeJsonResult()` calls the same detector and writes the four keys.

### Unix

```c
static int ttyfd = STDOUT_FILENO;
if (!isatty(ttyfd)) ttyfd = open("/dev/tty", O_RDWR | O_NOCTTY | O_CLOEXEC);
ioctl(ttyfd, TIOCGWINSZ, &winsize);
```

The `ioctl` result is not checked; the decision is made from the returned structure. If
`ws_row`/`ws_col` are `0`, `ffGetTerminalResponse("\e[18t", 2, "\e[8;%hu;%hut", …)` fills rows and
columns from the terminal's reply. If `ws_ypixel`/`ws_xpixel` are `0`,
`ffGetTerminalResponse("\e[14t", 2, "\e[4;%hu;%hut", …)` fills the pixel pair. The function then
fails only when both character dimensions are still `0`.

### Windows

`GetStdHandle(STD_OUTPUT_HANDLE)` is used when `GetConsoleMode()` succeeds; otherwise `CONOUT$` is
opened. `GetConsoleScreenBufferInfo()` supplies `Right - Left + 1` and `Bottom - Top + 1`; if that
fails, the `\e[18t` query is used with a format that skips the leading fields
(`%*[^;];%hu;%hut`). The pixel size then comes from `GetCurrentConsoleFontEx()` multiplied by the
character counts, with the `\e[14t` query as the fallback. The module reports success only when
both `columns` and `rows` are greater than zero.
