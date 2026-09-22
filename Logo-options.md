This document describes the options that control the logo, and the logo types that can be selected with `--logo-type`.

## General

| Option | Default | Description |
|--------|---------|-------------|
| `--logo`, `-l` | `""` | The logo source, interpreted according to the type |
| `--logo-type` | `auto` | The logo type. See the following categories for possible values |
| `--<type>` | `""` | Shorthand for `--logo-type <type> --logo <value>` |
| `--logo-padding` | `""` | Sets the padding left and right of the logo |
| `--logo-padding-left` | `0` | Sets the padding to the left of the logo |
| `--logo-padding-right` | `4` | Sets the padding to the right of the logo |
| `--logo-padding-top` | `0` | Sets the padding above the logo |
| `--logo-padding-bottom` | `0` | Sets the padding below the logo |
| `--logo-position` | `left` | Sets the position to display the logo |

`--logo-padding-top` and `--logo-padding-bottom` only take effect with `--logo-position top`: they add blank lines above the logo, and between the logo and the first module.

All filesystem paths can be:

* Absolute. Environment variables and `~` are allowed. See below.
* Relative to the current working directory.

Note when using environment variables in config files:

1. On Windows, only `%ENV_VAR%` syntax is supported, for example `%USERPROFILE%/path/to/logo file.txt`. See [`ExpandEnvironmentStringsA`](https://learn.microsoft.com/en-us/windows/win32/api/processenv/nf-processenv-expandenvironmentstringsa) for details.
    * In [v2.41.0](https://github.com/fastfetch-cli/fastfetch/releases/tag/2.41.0) or newer, `~` expansion is supported.
2. On *nix, all special characters (` |&;<>(){}`) must be escaped with `\` or quoted with `"`, for example `~/path/to/logo\ file.txt` or `$HOME/path/to/"logo file".txt`. See [`wordexp`](https://man7.org/linux/man-pages/man3/wordexp.3.html) for details.
    * If you run a script that prints a path that contains white spaces, [it must also be quoted](https://github.com/fastfetch-cli/fastfetch/discussions/1707#discussioncomment-12904822).
    * Command substitution is allowed
    * If the expression expands to multiple files, fastfetch will randomly use one. For example, you can use `"source": "~/path/to/images/*.png"` to randomly choose an image to display.

## Text

| Option | Default | Description |
|--------|---------|-------------|
| `--logo-color-[1-9]` | `""` | Overrides a color for logos that support it |
| `--logo-print-remaining` | `true` | Print the remaining logo if it is higher than the keys |

* Color placeholders are in the form `$[1-9]`. To print a `$`, use `$$`.
* If a color placeholder value is not set, the placeholder is simply discarded.

## Image

| Option | Default | Description |
|--------|---------|-------------|
| `--logo-width` | `""` | Sets the width of the logo for logos that support it |
| `--logo-height` | `""` | Sets the height of the logo for logos that support it |
| `--logo-preserve-aspect-ratio` | `false` | Fills the given width and height as much as possible without stretching. Supported by the `iterm` logo type only |
| `--logo-cache` | `true` | Sets how the image logo cache is used |
| `--logo-animation-frame` | `1` | Selects which frame of an animated image logo is rendered |

* If only one of the height/width options is set, the aspect ratio is preserved.
* If neither height nor width is set, the original size is used.

### Logo cache

`--logo-cache` accepts:

| Value | Description |
|-------|-------------|
| `true` | Reuses a cached rendering when it is valid, and writes it back on a cache miss |
| `false` | Ignores the image logo cache completely: nothing is read from it and nothing is written to it |
| `regen` | Ignores any existing cached rendering and regenerates it |

* A cached rendering is keyed on the source path and the requested pixel size, and is validated against the modification time of the source image. Editing an image in place therefore invalidates its cached rendering, but changing an option that affects the rendering without touching the file, such as `--logo-type`, does not. Run `--logo-cache regen` after such a change.
* Rendering a frame other than the first is cached as well, and shares the entry of the first frame. The frame number is recorded per cache directory rather than per logo type, so switching `--logo-type` between two types that both have a cached rendering here, and then asking for a different frame, can pair one type's rendering with the other's frame number. Run `--logo-cache regen` after such a switch.

### Animation

`--logo-animation-frame` selects which frame of an animated image (GIF or APNG) is rendered:

| Value | Description |
|-------|-------------|
| `1` | The default. Renders the first frame as a still image |
| `0` | Plays the animation |
| `N` | Renders the Nth frame as a still image |
| `-N` | Renders the Nth frame from the end as a still image, so `-1` is the last frame |

* The `sixel`, `kitty` and `chafa` logo types decode the image themselves, so fastfetch can render any single frame of an animated source.
* Only the `kitty` logo type plays an animation that fastfetch decodes and composes itself. No external program is involved.
* The `iterm` and `kitty-direct` logo types play an animation too, but the terminal does it: the source file is passed through unchanged, and a terminal that understands animated GIFs animates it on its own. fastfetch cannot control that animation, so the value of `--logo-animation-frame` has no effect. Whether APNG sources are animated as well depends on the terminal and has not been tested.
* The `kitty-icat` logo type hands the source to `kitten icat`, which does its own animation handling, and the `raw` logo type prints the file as-is. `--logo-animation-frame` has no effect on either.
* The terminal-side players accept GIF. To play a video in another format, convert it to a GIF first.
* A value outside the range of the source is clamped to the first or the last frame instead of being rejected.
* Negative values can only be given in the JSON config (`logo.animationFrame`), as the command line parser reads a leading `-` as another option.
* A source that has a single frame is rendered as a still image even when `0` is given.
* The frames come from the platform image framework: WIC on Windows, ImageIO on macOS, AImageDecoder on Android, and ImageMagick on Linux and BSD.

# Logo types

## auto

Tries to detect the logo type based on the value of `--logo`, in the following order:
* If the value is empty/not set, an auto-detected builtin logo is displayed.
* If the value is the name of a builtin logo, it is displayed.
* If the value is the path to a file whose extension is `.raw`, it is displayed without post-processing.
* If the value is the path to a file whose extension is not `.txt`, and the terminal emulator is known to support an image protocol, the file is displayed as an image.
    * This behavior is for compatibility only. It's known to be buggy for some terminals. Setting an explicit image protocol is highly recommended.
* If the value is the path to a text file, its content is displayed with color tag replacement.
* Otherwise, the auto-detected builtin logo is displayed. In this case, use `--show-errors` to find what's going wrong.

## builtin

The value of `--logo` is interpreted as the name of a builtin logo.
* If the value is empty/not set, it is auto-detected.
* Use `--list-logos` to get a list of all available logos.
* Use `--print-logos` to see all available logos.
* All builtin logos set default values for `--logo-color-[1-9]`.
* Use `none` to disable the logo but keep key colors from the detected logo.

## small

Like `builtin`, but search for small logos. For example, if you are running macOS:

* default -> macos
* `-l small` -> macos_small
* `--logo-type small` -> macos_small
* `-l macos` -> macos
* `-l macos --logo-type small` -> macos_small
* `-l macos_small` -> macos_small
* `-l macos_small --logo-type small` -> macos_small, as the name is matched exactly as well

`-l small` is also accepted as a value of `-l` itself, as a shortcut for `--logo-type small`.

A name that matches no builtin logo, such as `macos_small_small`, is reported as a failure with `--show-errors` and falls back to the auto-detected logo.

## file

The value of `--logo` is interpreted as the path to a file. The content of the file is displayed.
* Color placeholders ($[1-9]) are replaced with color codes specified in `--logo-color-[1-9]`.
* If the file can't be read, the auto-detected builtin logo is displayed.

## file-raw

The value of `--logo` is interpreted as the path to a file. The content of the file is displayed.
* No color placeholder replacement is performed.
* If the file can't be read, the auto-detected builtin logo is displayed.
* Single dash (`-`) is an alias for `/dev/stdin`, which can be used to pipe another program's output to fastfetch. For example:
    * [Display a pokemon sprite](https://github.com/talwat/pokeget-rs): `pokeget random --hide-name | fastfetch --file-raw -`

## command-raw

Supported in JSONC config only. Run a shell command specified by `logo.source` and display its output.
* Useful for displaying custom logos generated by other programs such as [`pokeget`](https://github.com/talwat/pokeget-rs).
* `{ "type": "command-raw", "source": "pokeget random --hide-name" }`

## data

The value of `--logo` is interpreted as the logo content and directly displayed.
* Color placeholders ($[1-9]) are replaced with color codes specified in `--logo-color-[1-9]`.
* If the value is empty/not set, the auto-detected builtin logo is displayed.

## data-raw

The value of `--logo` is interpreted as the logo content and directly displayed.
* No color placeholder replacement is performed.
* If the value is empty/not set, the auto-detected builtin logo is displayed.

## sixel

The value of `--logo` is interpreted as the path to an image file. It is displayed using the sixel graphics protocol.
* On Linux and BSD, `fastfetch` must be compiled with the `imagemagick6` or `imagemagick7` feature.
* On Windows, macOS and Android, the image is decoded by the platform image framework and encoded by an embedded libsixel encoder, so no external library is needed. The encoder is reported by `fastfetch --list-features` as `Embedded sixel`.
* Has the widest terminal support.

See also [this issue](https://github.com/fastfetch-cli/fastfetch/issues/656#issuecomment-1849448969) for rendering transparent backgrounds.

## kitty

The value of `--logo` is interpreted as the path to an image file. It is displayed using the kitty graphics protocol.
* On Linux and BSD, `fastfetch` must be compiled with the `imagemagick6` or `imagemagick7` feature. On Windows, macOS and Android the platform image framework is used instead.
* This is the only logo type whose animation is decoded and composed by fastfetch. See [`--logo-animation-frame`](#animation).

## kitty-direct

The value of `--logo` is interpreted as the path to an image file. It is displayed using the kitty graphics protocol, and the image is directly sent to the terminal emulator.
* Set both `--logo-width` and `--logo-height`, so that fastfetch knows how many columns and rows the image occupies without having to ask the terminal for the cursor position afterwards.
* This is by far the fastest image protocol because the image content is loaded directly by the terminal rather than being encoded and passed through tty. We suggest using it if supported by your terminal.
* Known to work with wezterm and warp (`png`, `jpg`, `webp` and maybe more); kitty and ghostty (`png` only).
* An animated GIF is passed through unchanged and played by the terminal. See [Animation](#animation).
* Note: konsole (up to 23.04) is said to support the `kitty` image protocol, but it doesn't support `kitty-direct`.

## kitty-icat

The value of `--logo` is interpreted as the path to an image file. It is displayed by running [`kitten icat`](https://sw.kovidgoyal.net/kitty/kittens/icat/), which ships with kitty.
* Requires the `kitten` binary to be available in `PATH`. fastfetch itself does not decode the image, so neither ImageMagick nor any other image library is needed.
* `kitten icat` positions the image itself, so `--logo-position right` is not supported, and `--logo-position top` is only supported when `--logo-width` is not set.
* Known to have issues inside tmux, where `kitten icat` switches to unicode placeholders.

## iterm

The value of `--logo` is interpreted as the path to an image file. It is displayed using the iTerm graphics protocol.
* Set both `--logo-width` and `--logo-height`, so that fastfetch knows how many columns and rows the image occupies without having to ask the terminal for the cursor position afterwards.
* Known to work with wezterm (`kitty-direct` is preferred), iterm2, and konsole.
* An animated GIF is passed through unchanged and played by the terminal. See [Animation](#animation).

## chafa

The value of `--logo` is interpreted as the path to an image file. It is converted to ASCII art using `libchafa`.
* `fastfetch` must be compiled with the `chafa` feature.
* On Linux and BSD, `fastfetch` must also be compiled with the `imagemagick6` or `imagemagick7` feature. On Windows, macOS and Android the platform image framework is used instead.
* Use `--chafa-fg-only` to set whether to render foreground only.
* Use `--chafa-symbols` to set the symbols.
* Use `--chafa-canvas-mode` to set the canvas mode.
* Use `--chafa-color-space` to set the color space.
* Use `--chafa-dither-mode` to set the dither mode.
* See the [chafa documentation](https://hpjansson.org/chafa/man/) for more information.

## raw

The value of `--logo` is interpreted as the path to a binary file. It is printed as-is.
* Set both `--logo-width` and `--logo-height`, with the actual size used by the logo, so that fastfetch knows how many columns and rows the image occupies without having to ask the terminal for the cursor position afterwards.
* Use this to display a pre-converted image file.
* If the file can't be read, the auto-detected builtin logo is displayed.

This is the preferred way to display sixel images. For example:

```
$ # make sure libsixel is installed
$ img2sixel /path/to/image > image.sixel # convert an image to sixel format
$ cat image.sixel && echo END # check if it works fine. `END` should be printed at the right bottom corner of the image
$ fastfetch --raw image.sixel # optionally set --logo-width $NUMBER_OF_COLUMNS_USED --logo-height $NUMBER_OF_ROWS_USED
```

Some terminals provide `imgcat`. It can be used for `--raw` too:

```
$ imgcat /path/to/image > image.bin # convert an image to whatever format the terminal supports
$ cat image.bin && echo END
$ fastfetch --raw image.bin # optionally set --logo-width $NUMBER_OF_COLUMNS_USED --logo-height $NUMBER_OF_ROWS_USED
```

When working with kitty:

```
$ kitten icat -n --align=left --transfer-mode=stream /path/to/image > image.bin
$ cat image.bin && echo END
$ fastfetch --raw image.bin
```

## none

Disables logo printing.

## Media cover

In v2.55.0 and newer, fastfetch accepts a special logo source keyword `media-cover`, which displays the cover art of the currently playing media. If used, `--logo-type` should be also be set to the desired image protocol supported by your terminal.

Shortcut options, such as `--iterm media-cover`, are also supported and recommended.

## Troubleshooting

1. My logo won't display: Try `--pipe false`. Fastfetch detects if its output is piped (stdout is not a tty); in this case, all colors and logo will be removed. See `fastfetch -h pipe`.
2. Image collapses command line text: This is a known issue. Try specifying `--logo-width` and `--logo-height`, or prefix `fastfetch` with `clear && ` to clear the screen before printing logos (this matches the behavior of neofetch).
3. My image logo won't display and the default ASCII is displayed instead: Likely some errors occurred. Try specifying `--show-errors` to see the error message.
4. My animated logo shows a still image: The default of `--logo-animation-frame` is `1`, which renders a still image. Set it to `0` to play the animation. See [Animation](#animation).
5. The top of my image logo is scrolled away: An image logo is drawn at its natural size, so the terminal has to make room for it. When the image is taller than the terminal, the terminal scrolls the screen while drawing it and the top rows of the logo end up outside the viewport. The `iterm` and `kitty-direct` logo types also clear the scrollback when they are used with `--logo-position left` or `right`, so those rows cannot be scrolled back to. Use a smaller image, or set both `--logo-width` and `--logo-height` so that the terminal scales the image down to a size that fits.
