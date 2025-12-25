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
| `--logo-position` | `left` | Sets the position to display the logo |

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

* If only one of the height/width options is set, the aspect ratio is preserved.
* If neither height nor width is set, the original size is used.

# Logo types

## auto

Tries to detect the logo type based on the value of `--logo`, in the following order:
* If the value is empty/not set, an auto-detected builtin logo is displayed.
* If the value is the name of a builtin logo, it is displayed.
* If the value is the path to a text file (whose extension is `.txt`), the content is displayed with color tag replacement.
* If the value is the path to a raw image file (whose extension is `.raw`), the content is displayed without post-processing.
* If the value is the path to a file which is neither txt nor raw, it's assumed as an image file. If the terminal emulator is known to support an image protocol, the image is displayed.
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
* `-l macos_small --logo-type small` -> error, because Logo `macos_small_small` doesn't exist

## file

The value of `--logo` is interpreted as the path to a file. The content of the file is displayed.
* Color placeholders ($[1-9]) is replaced with color codes specified in `--logo-color-[1-9]`.
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
* Color placeholders ($[1-9]) is replaced with color codes specified in `--logo-color-[1-9]`.
* If the value is empty/not set, the auto-detected builtin logo is displayed.

## data-raw

The value of `--logo` is interpreted as the logo content and directly displayed.
* No color placeholder replacement is performed.
* If the value is empty/not set, the auto-detected builtin logo is displayed.

## sixel

The value of `--logo` is interpreted as the path to an image file. It is displayed using the sixel graphics protocol.
* `fastfetch` must be compiled with the `imagemagick6` or `imagemagick7` feature.
* Has the widest terminal support.

See also [this issue](https://github.com/fastfetch-cli/fastfetch/issues/656#issuecomment-1849448969) for rendering transparent backgrounds.

## kitty

The value of `--logo` is interpreted as the path to an image file. It is displayed using the kitty graphics protocol.
* `fastfetch` must be compiled with the `imagemagick6` or `imagemagick7` feature.

## kitty-direct

The value of `--logo` is interpreted as the path to an image file. It is displayed using the kitty graphics protocol, and the image is directly sent to the terminal emulator.
* Both `--logo-width` and `--logo-height` must be specified.
* This is by far the fastest image protocol because the image content is loaded directly by the terminal rather than being encoded and passed through tty. We suggest using it if supported by your terminal.
* Known to work with wezterm and warp (`png`, `jpg`, `webp` and maybe more); kitty and ghostty (`png` only).
* Note: konsole (up to 23.04) is said to support the `kitty` image protocol, but it doesn't support `kitty-direct`.

## iterm

The value of `--logo` is interpreted as the path to an image file. It is displayed using the iTerm graphics protocol.
* Both `--logo-width` and `--logo-height` must be specified.
* Known to work with wezterm (`kitty-direct` is preferred), iterm2, and konsole.

## chafa

The value of `--logo` is interpreted as the path to an image file. It is converted to ASCII art using `libchafa`.
* `fastfetch` must be compiled with the `chafa` feature.
* `fastfetch` must be compiled with the `imagemagick6` or `imagemagick7` feature.
* Use `--chafa-fg-only` to set whether to render foreground only.
* Use `--chafa-symbols` to set the symbols.
* Use `--chafa-canvas-mode` to set the canvas mode.
* Use `--chafa-color-space` to set the color space.
* Use `--chafa-dither-mode` to set the dither mode.
* See the [chafa documentation](https://hpjansson.org/chafa/man/) for more information.

## raw

The value of `--logo` is interpreted as the path to a binary file. It is printed as-is.
* Both `--logo-width` and `--logo-height` must be specified. Their value must be the actual size used by the logo.
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

## Animation

Kitty (with `kitty-icat` protocol) and iTerm (with `iterm` protocol) support gifs. If you have videos with other formats, you must convert them into gifs first.
