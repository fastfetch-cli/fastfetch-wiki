# Global

> The shared model behind every module page in `Modules/`

fastfetch is a list of **modules**: each one collects a piece of system information and prints one
or more lines (`OS: Linux 6.8.0`, `CPU: AMD Ryzen 7 5800X (16) @ 3.80 GHz`). This page explains how the
modules fit together, what they have in common, and what conventions the pages in this folder
follow. Read it once and the individual pages become much easier to navigate.

Aimed at three audiences, in this order: people who *use* fastfetch, people who want to *extend*
it, and maintainers who need to recall a detail quickly. Every module page is organised the same
way, with the user-facing parts first.

## At a glance

| | |
|---|---|
| Modules registered | 76 (`fastfetch --list-modules`) |
| Module source | `src/modules/<name>/` — one directory per module |
| Detection source | `src/detection/<name>/` — one directory per subsystem |
| Supported platforms | Linux, Android, FreeBSD (incl. MidnightBSD/DragonFly), NetBSD, OpenBSD, Solaris/illumos, Haiku, GNU/Hurd, macOS, Windows |
| Default structure | `fastfetch --print-structure` — 29 modules, see below |
| Options for a module | JSON config only; the per-module command line flags were removed in 2.52.0 |
| Format help | `fastfetch -h <type>-format`, e.g. `fastfetch -h disk-format` |

## Running fastfetch

With no arguments fastfetch prints the default structure (29 modules) next to a logo. `--help`
renders the whole command line from `doc/help.json` and groups it exactly the way the config does:

| Group | Options | Example |
|---|---|---|
| Informative | `-h`, `-v`, `--list-*`, `--print-*`, `--format`, `--dynamic-interval` | `--list-modules` |
| Config | `-c` / `--config`, `--gen-config` | `-c examples/18.jsonc` |
| General | `--thread`, `--wmi-timeout`, `--processing-timeout`, `--ds-force-drm`, `--detect-version` | `--detect-version false` |
| Logo | `-l` / `--logo` plus the 30 other options in its group (`--logo-*`, `--file`, `--data`, `--raw`, `--sixel`, `--kitty*`, `--iterm`, `--chafa-*`) | `-l none` |
| Display | `-s` / `--structure`, `--pipe`, `--stat`, colours, percentages, bars, units | `-s os:kernel` |

The one that matters most for modules is `-s`, a colon-separated list of module names:

```bash
fastfetch -s os:kernel:uptime                        # exactly these three
fastfetch --print-structure                          # the built-in default, and -s's default
fastfetch -s os:kernel:uptime --structure-disabled kernel   # drop one from the list
```

Three things surprise people here:

- **A name that is not a module is dropped silently.** `-s os:nosuchmodule` prints the `OS` line and
  exits 0 — no warning, no error. A typo is invisible; check against `--list-modules`.
- **`-` is not a "disable" marker.** `-s os-kernel` looks for a module called `os-kernel`, finds
  none, and prints nothing at all. `--structure-disabled` is the only way to remove a module.
- **`--structure` bypasses the config entirely**, so per-module options cannot be used with it. The
  `command` module refuses outright rather than printing something wrong, and the process exits with
  status 481 — which a shell reports as 225:

  ```
  Error: module `command` is not supported with `--structure`
         Its options can only be read from the JSON config, which `--structure` bypasses.
         Add it to the config instead, e.g. `{ "modules": [ { "type": "command", "text": "uname -r" } ] }`
  ```

Two switches change what the output *is* rather than what it contains:

```bash
fastfetch --format json -s os:kernel     # one JSON document; -j is the short form
fastfetch --stat -s cpu:cpuusage:diskio  # append each module's own timing to its line
```

`--format json` and `--dynamic-interval` are mutually exclusive:
`Error: --dynamic-interval cannot be used with --json`, exit 400 (144 as seen by a shell).

Two environment variables are read before any module runs:

- **`NO_CONFIG`** skips the automatic config lookup. An explicit `-c` still loads, because that has
  already marked the config as loaded.
- **`NO_COLOR`** sets `display.pipe` to `true` — the same switch as `--pipe`, so it *does* turn colour
  off in normal output; an explicit `--pipe false` is what overrides it. It also forces `--gen-config`
  down the non-interactive path (below).

## Generating and customising a configuration

The config is JSONC and is read from the first `fastfetch/config.jsonc` found in the search paths
(`--list-config-paths`; `(*)` marks the directories that actually hold one), usually
`~/.config/fastfetch/config.jsonc`. `--list-data-paths` shows where presets and logos are looked up.

```bash
fastfetch --gen-config                 # interactive UI in a terminal; writes the default path
fastfetch --gen-config /tmp/x.jsonc    # explicit path; non-interactive when not a TTY
fastfetch --gen-config - -s os         # print to stdout instead of writing a file
```

The interactive UI is only entered when **both** stdin and stdout are attached to a real console, so
redirecting either one — or setting `NO_COLOR` — makes the file be written straight away, which is
what a script wants. Redirecting to `NUL` or `/dev/null` counts as a redirect, not as a terminal.

`--gen-config` **refuses to overwrite an existing file**:

```
Error: file `/tmp/x.jsonc` exists. Please remove it before generating a new one
```

That is deliberate, and it is also a trap: the default path is the real config, so running
`--gen-config` without a path on a machine that already has one writes nothing at all. Pass an
explicit path while experimenting.

What it writes is minimal — one entry per selected module, which is exactly the shape the JSON schema
documents:

```jsonc
{
  "$schema": "https://github.com/fastfetch-cli/fastfetch/raw/master/doc/json_schema.json",
  "modules": [ "os" ]
}
```

The `$schema` line is what gives an editor completion and validation; keeping it pointed at the
`dev`/`master` schema is what makes the editor catch an option the running binary would reject.

Three ways to get started:

| Route | How |
|---|---|
| A preset | `fastfetch -c neofetch` — a **name lookup** in the data paths, not a relative path |
| A shipped example | `fastfetch -c examples/18.jsonc` — same lookup; `--list-presets` prints the tree |
| From scratch | `fastfetch --gen-config /tmp/x.jsonc`, then edit |

Customisation happens at four levels, and mixing them up is the usual source of confusion:

| Level | Where | Affects |
|---|---|---|
| Global | the top-level `general` / `logo` / `display` objects | every module |
| Module | one object inside `modules[]` | that module only |
| Format | `format` on a module | the value text only, not the key |
| Key | `key`, `keyColor`, `keyIcon`, `keyWidth`, `outputColor` | the key block only |

An unknown key is an error and fastfetch names it — but that message is itself suppressed by default,
so a config that appears to run fine can still have been misread. See `display.showErrors` under
*Global settings that change module output*. `fastfetch -h <option>` explains a single option,
`Configuration` is the narrative reference, and `Json-Schema` is the machine-readable one.

Since 2.52.0 there are **no per-module command line options at all**: `--cpu-format`, `--disk-*` and
the rest are gone, and a JSON config is the only way to configure a module. `--gen-config` is
therefore not a convenience but the supported way to discover the keys a module accepts.

## Anatomy: two layers

Every module is split into two layers, and keeping them apart explains most of the behaviour you
will see:

1. **The module layer** (`src/modules/<name>/<name>.c`) owns the *user interface*: the JSON options
   (`option.h`), the format string, the key/icon, the console output and the JSON result. It never
   touches the operating system directly.
2. **The detection layer** (`src/detection/<name>/`) owns the *operating system access*. It fills a
   plain result struct (`FFFooResult`) and returns `const char*` — `nullptr` on success, or an error
   message such as `"Not supported on this platform"`.

A module without an OS dependency (e.g. `Title`, `Separator`, `DateTime`, `Custom`, `Break`) simply
has no detection layer, and a module may reuse a detection subsystem that carries another name —
`Display` and `Monitor` both sit on `src/detection/displayserver/`, `Shell`/`Terminal` on
`terminalshell`, `Player` on `media`, `Version` on `libc`.

For each platform the build selects exactly one implementation file per subsystem, named after the
platform (`battery_linux.c`, `battery_apple.c`, `battery_windows.c`, …). Platforms that have no
implementation get an explicit stub, `battery_nosupport.c`, which returns
`"Not supported on this platform"`:

```c
const char* ffDetectBattery(FFBatteryOptions* options, FFlist* results) {
    FF_UNUSED(options, results)
    return "Not supported on this platform";
}
```

That is why "unsupported" is a *runtime* message rather than a build error: the module is still
there, it just refuses to produce data.

## How a run is assembled

`main()` in `src/fastfetch.c` is short, and the order in it explains a lot of observable behaviour:

1. `ffInitInstance()` — one global `instance` holding the parsed options, the platform state and the
   caches; `atexit(ffDestroyInstance)` tears it down.
2. **First pass** over `argv` (`parseCommand`) — only the options that decide what is read at all:
   `-c`, `-s`, `--gen-config`, and the `--list-*` queries.
3. `parseConfigFiles()` — unless `NO_CONFIG` is set or `-c` already loaded one.
4. **Second pass** over `argv` (`parseOption`) — the real options, which now override the config.
5. `run()` — the fetch, or `--gen-config`'s `writeConfigFile()`.

`run()` then has two mutually exclusive output paths:

| Condition | Prepare pass | Print pass |
|---|---|---|
| A JSON config was loaded and `--structure` was not given | `ffPrintJsonConfig(data, prepare = true)` | `ffPrintJsonConfig(data, prepare = false)` |
| Otherwise (structure-based) | `ffPrepareCommandOption()` | `ffPrintCommandOption()` |

**The two passes are the single most important structural fact about this project.** The prepare pass
walks the module list, reads the options and starts the work that has to begin early — spawning the
`command` module's shells, taking the first of the two samples the rate modules need — and it
evaluates `condition` there too, so a module skipped in prepare is skipped in print as well.

Two consequences worth remembering when something looks wrong:

- Work done in the wrong pass shows up as *duplication*: a validation message printed twice, an HTTP
  request issued twice, a `--gen-config` field written twice.
- A module that returns `false` from `printModule` while still having printed successfully will
  suppress every later module gated on `condition.succeeded`, because that return value is what the
  next module's `condition` reads.

`ffStart()` sits *between* the two passes, and when the output is not JSON `ffLogoPrint()` writes the
logo there. With `--dynamic-interval` the prepare/print pair becomes a loop: after each print pass
fastfetch sleeps, moves the cursor back to the top of the block it just wrote, calls
`ffCacheInvalidateAll()` so that the detection results cached for the round are rebuilt instead of
replayed, and runs the prepare pass again.

### Threads, caches and the environment

- `general.thread` moves the blocking HTTP requests onto worker threads, so a slow endpoint does not
  stall the whole run.
- There are two unrelated caching mechanisms, and they answer different questions:
  - `FFcache` (`src/common/FFcache.h`) holds a value that is **built at most once per generation**.
    `--dynamic-interval` starts a new generation per round and drops everything built so far, which
    is why `Display`, `Monitor`, `WM`, `DE`, `Media`, `Player`, `Shell` and `Terminal` re-detect
    instead of replaying the first round. It is explicitly *not* for the baselines the rate modules
    derive their numbers from (`cpuusage`, `diskio`, `netio`, `top`): dropping a baseline makes the
    first sample of the round read as zero.
  - Two caches are keyed on the source file's **mtime** and therefore survive across runs: the image
    logo cache (`src/logo/image/`) and the package-count cache (`src/detection/packages/`).
- `NO_CONFIG` skips config loading, `NO_COLOR` only affects `--gen-config`, and `--pipe` disables
  colour in the output.

## Module catalogue

`Order` is the module's `defaultOrder`. It is **not** the order in which modules are printed — it
only drives the sorting of `fastfetch --gen-config` and the interactive config generator. Modules
without a number are never part of a generated config.

| Module | `type` | Order | Description |
|---|---|---|---|
| [Title](Modules/Title) | `title` | 1 | Print the title, including your username and hostname |
| [Separator](Modules/Separator) | `separator` | 2 | Print a separator line |
| [OS](Modules/OS) | `os` | 3 | Print the OS or Linux distribution name and version |
| [Host](Modules/Host) | `host` | 4 | Print your computer's product name |
| [BIOS](Modules/BIOS) | `bios` | 5 | Print first-stage bootloader information (name, version, release date, etc.) |
| [Bootmgr](Modules/Bootmgr) | `bootmgr` | 6 | Print second-stage bootloader information (name, firmware, etc.) |
| [Board](Modules/Board) | `board` | 7 | Print motherboard name and other information |
| [Chassis](Modules/Chassis) | `chassis` | 8 | Print chassis type information (desktop, laptop, etc.) |
| [Kernel](Modules/Kernel) | `kernel` | 9 | Print system kernel version |
| [InitSystem](Modules/InitSystem) | `initsystem` | 10 | Print init system (pid 1) name and version |
| [Uptime](Modules/Uptime) | `uptime` | 11 | Print how long the system has been running |
| [Loadavg](Modules/Loadavg) | `loadavg` | 12 | Print system load averages |
| [Processes](Modules/Processes) | `processes` | 13 | Print number of running processes and threads |
| [Packages](Modules/Packages) | `packages` | 14 | List installed package managers and count of installed packages |
| [Shell](Modules/Shell) | `shell` | 15 | Print the current shell name and version |
| [Editor](Modules/Editor) | `editor` | 16 | Print information about the default editor ($VISUAL or $EDITOR) |
| [Display](Modules/Display) | `display` | 17 | Print resolutions, refresh rates, etc |
| [Brightness](Modules/Brightness) | `brightness` | 18 | Print the current brightness level of your monitors |
| [Monitor](Modules/Monitor) | `monitor` | 19 | Same as Display module, but with a different default output format |
| [LM](Modules/LM) | `lm` | 20 | Print login manager (desktop manager) name and version |
| [DE](Modules/DE) | `de` | 21 | Print desktop environment name |
| [WM](Modules/WM) | `wm` | 22 | Print the window manager name and version |
| [WMTheme](Modules/WMTheme) | `wmtheme` | 23 | Print the current window manager theme |
| [Theme](Modules/Theme) | `theme` | 24 | Print the current desktop environment theme |
| [Icons](Modules/Icons) | `icons` | 25 | Print icon style name |
| [Font](Modules/Font) | `font` | 26 | Print system font names |
| [Cursor](Modules/Cursor) | `cursor` | 27 | Print cursor style name |
| [Wallpaper](Modules/Wallpaper) | `wallpaper` | 28 | Print the file path of the current wallpaper |
| [Terminal](Modules/Terminal) | `terminal` | 29 | Print the current terminal name and version |
| [TerminalFont](Modules/TerminalFont) | `terminalfont` | 30 | Print the font name and size used by the current terminal |
| [TerminalSize](Modules/TerminalSize) | `terminalsize` | 31 | Print the current terminal size |
| [TerminalTheme](Modules/TerminalTheme) | `terminaltheme` | 32 | Print the current terminal theme (foreground and background colors) |
| [CPU](Modules/CPU) | `cpu` | 33 | Print CPU name, frequency, etc. |
| [CPUCache](Modules/CPUCache) | `cpucache` | 34 | Print CPU cache sizes |
| [CPUUsage](Modules/CPUUsage) | `cpuusage` | 35 | Print CPU usage. Collecting data takes some time |
| [GPU](Modules/GPU) | `gpu` | 36 | Print GPU names, memory sizes, types, etc |
| [Top](Modules/Top) | `top` | 37 | Print processes with the highest CPU, memory or disk I/O usage |
| [Codec](Modules/Codec) | `codec` | 38 | Print hardware video acceleration codec types (decode / encode) |
| [Memory](Modules/Memory) | `memory` | 39 | Print system memory usage information |
| [PhysicalMemory](Modules/PhysicalMemory) | `physicalmemory` | 40 | Print system physical memory devices |
| [Swap](Modules/Swap) | `swap` | 41 | Print swap (paging file) space usage |
| [Disk](Modules/Disk) | `disk` | 42 | Print partitions, space usage, file system, etc |
| [Btrfs](Modules/Btrfs) | `btrfs` | 43 | Print Linux BTRFS volumes |
| [Zpool](Modules/Zpool) | `zpool` | 44 | Print ZFS storage pools |
| [Battery](Modules/Battery) | `battery` | 45 | Print battery information |
| [PowerAdapter](Modules/PowerAdapter) | `poweradapter` | 46 | Print power adapter name and charging watts |
| [Player](Modules/Player) | `player` | 47 | Print the music player name that is currently active |
| [Media](Modules/Media) | `media` | 48 | Print the name of the currently playing song |
| [PublicIp](Modules/PublicIp) | `publicip` | 49 | Print your public IP address and related information |
| [LocalIp](Modules/LocalIp) | `localip` | 50 | List local IP addresses (IPv4 or IPv6), MAC addresses, etc |
| [DNS](Modules/DNS) | `dns` | 51 | Print configured DNS servers |
| [Wifi](Modules/Wifi) | `wifi` | 52 | Print connected Wi-Fi info (SSID, connection and security protocol) |
| [DateTime](Modules/DateTime) | `datetime` | 53 | Print the current date and time |
| [Locale](Modules/Locale) | `locale` | 54 | Print system locale name |
| [Vulkan](Modules/Vulkan) | `vulkan` | 55 | Print the highest Vulkan version supported by the GPU |
| [OpenGL](Modules/OpenGL) | `opengl` | 56 | Print the highest OpenGL version supported by the GPU |
| [OpenCL](Modules/OpenCL) | `opencl` | 57 | Print the highest OpenCL version supported by the GPU |
| [Users](Modules/Users) | `users` | 58 | Print users who are currently logged in |
| [Bluetooth](Modules/Bluetooth) | `bluetooth` | 59 | List connected Bluetooth devices |
| [BluetoothRadio](Modules/BluetoothRadio) | `bluetoothradio` | 60 | List Bluetooth radios (supported versions, vendors, etc.) |
| [Sound](Modules/Sound) | `sound` | 61 | Print sound devices, volume levels, etc |
| [Camera](Modules/Camera) | `camera` | 62 | Print available cameras |
| [Gamepad](Modules/Gamepad) | `gamepad` | 63 | List connected gamepads |
| [Mouse](Modules/Mouse) | `mouse` | 64 | List connected mice |
| [Keyboard](Modules/Keyboard) | `keyboard` | 65 | List connected keyboards |
| [Weather](Modules/Weather) | `weather` | 66 | Print weather information |
| [NetIO](Modules/NetIO) | `netio` | 67 | Print network I/O throughput |
| [DiskIO](Modules/DiskIO) | `diskio` | 68 | Print physical disk I/O throughput |
| [PhysicalDisk](Modules/PhysicalDisk) | `physicaldisk` | 69 | Print physical disk information |
| [TPM](Modules/TPM) | `tpm` | 70 | Print information about the Trusted Platform Module (TPM) security device |
| [Version](Modules/Version) | `version` | 71 | Print the Fastfetch version and build information |
| [Break](Modules/Break) | `break` | 72 | Print an empty line |
| [Colors](Modules/Colors) | `colors` | 73 | Display the terminal's 16-color palette |
| [Command](Modules/Command) | `command` | — | Run custom shell scripts |
| [Custom](Modules/Custom) | `custom` | — | Print a custom string, with or without key |
| [Logo](Modules/Logo) | `logo` | — | Query built-in logo for JSON output |

## Selecting and ordering modules

Modules are printed in the order they appear, and the list comes from one of three places:

- `fastfetch -c <file>` → the `modules` array of that config, in array order. An entry is either a
  bare string (`"os"`) or an object (`{ "type": "os", ... }`).
- `fastfetch -s <structure>` (`--structure`) → a colon separated list,
  `fastfetch -s title:os:cpu`. The default when nothing is given is:

  ```
  Title:Separator:OS:Host:Kernel:Uptime:Packages:Shell:Display:DE:WM:WMTheme:Theme:Icons:Font:Cursor:Terminal:TerminalFont:CPU:GPU:Memory:Swap:Disk:LocalIp:Battery:PowerAdapter:Locale:Break:Colors
  ```

  Print it again with `fastfetch --print-structure`. It is a build option
  (`-DDEFAULT_STRUCTURE=...`) and can therefore differ in downstream packages.
- Both: `--structure` wins over the config `modules` array for the *selection*, but module options
  still come from the config — `--structure` looks up the first entry in `modules` whose `type`
  matches, and prints that entry with its own options.

Related switches:

| Switch | Effect |
|---|---|
| `--structure-disabled <list>` | Skips the listed modules, even if they are in the structure/config |
| `-s <module>` | Run a single module — the quickest way to test one |
| `--list-modules` | Prints the registry: index, name, description |

A module is matched by name, case-insensitively, against the registry entry — `"battery"`,
`"Battery"` and `"BATTERY"` are the same module. Names contain **no separators**: it is
`physicalmemory`, not `physical-memory` or `physical_memory`. An unknown name is not an error
worth stopping for; the module is replaced by the message `<no implementation provided>` (console)
or `{"type": ..., "error": "Unknown module type"}` (JSON).

Two modules, `custom` and `logo`, can only be used in the object form — see
[Pitfalls](#pitfalls-that-bite-across-modules).

The same module may appear several times: `"modules": ["cpu", { "type": "cpu", "key": "CPU2" }]`
prints CPU twice with different keys.

## Options every module accepts

`condition` is accepted by all 76 modules. The five key/value options (`key`, `keyColor`, `keyIcon`,
`keyWidth`, `outputColor`) are accepted by 73 of them and `format` by 72: `break`, `logo` and
`separator` have no key block at all, and `colors` has a key block but no format string. `custom`
accepts `format` yet publishes no variable list, because the caller supplies the variables. The
shared keys are documented once in [Configuration](Configuration#module-configuration) — the short
version:

| Key | Type | Meaning |
|---|---|---|
| `key` | string | Replaces the module key. It is itself a format string, with `{index}`, `{icon}` and `{module-name}` available. A **single space** (`" "`) hides the key *and* the separator |
| `keyColor` | color | Overrides `display.color.keys` for this module only |
| `keyIcon` | string | Icon used when `display.key.type` includes the icon bit |
| `keyWidth` | integer | Overrides `display.key.width`; implemented as a cursor jump to column `keyWidth + logoWidth` |
| `outputColor` | color | Overrides `display.color.output` |
| `format` | string | The module's output format; `fastfetch -h <type>-format` lists its variables |
| `condition` | object | Shows the module only when `system`/`!system`, `arch`/`!arch` and `succeeded` match |

Everything else is module specific and listed on the module's page.

### `condition` in practice

```jsonc
{ "type": "battery",  "condition": { "!system": "Windows" } }
{ "type": "command",  "condition": { "system": [ "Linux", "Android" ], "arch": "aarch64" } }
{ "type": "weather",  "condition": { "succeeded": true } }   // skip if the previous module failed
```

`system` is compared, case-insensitively, against the value the *running* fastfetch was built for
(`Linux`, `Android`, `FreeBSD`, `MidnightBSD`, `DragonFly`, `OpenBSD`, `NetBSD`, `SunOS`, `Haiku`,
`macOS`, `Windows`, `GNU`); `arch` against `x86_64`, `aarch64`, `arm`, `riscv`, `loongarch`, …;
`succeeded` chains to the preceding module in the list. The schema also accepts `null` for each of
them, which disables that single check.

## Global settings that change module output

These live outside the modules but are visible on every line, so they are worth knowing before
reading a module page:

| Setting | Effect on modules |
|---|---|
| `display.separator` | The string between key and value (default `": "`) |
| `display.key.type` | `none` / `string` / `icon` / `both` / `both-0` … — whether the key shows text, the icon, or both |
| `display.key.width` | Column the values are aligned to; `0` disables alignment |
| `display.color.*` | `keys`, `title`, `output`, `separator` |
| `display.percent` | The `type` (number/bar/both/…) and `green`/`yellow` thresholds used by every percentage module |
| `display.temp` | Unit and colouring of temperatures |
| `display.duration` | Human-readable formatting of every time span (uptime, media position, …) |
| `display.size.binaryPrefix` / `display.size.spaceBeforeUnit` | Unit system for sizes (`iec` KiB vs `si` kB vs `jedec` KB); the same `spaceBeforeUnit` also exists for `freq`, `duration` and `temp` |
| `display.stat` | Prints per-module execution time; in JSON mode adds a `stat` field |
| `display.showErrors` | **Off by default.** When off, module errors — including *typos in your config* — are silently swallowed. `--stat` turns it on as a side effect; otherwise set it explicitly |
| `display.pipe` | Disables all colours. It defaults to `!isatty(STDOUT_FILENO) \|\| getenv("NO_COLOR")`, so redirecting the output or setting the standard `NO_COLOR` variable is enough — no switch needed |
| `general.detect-version` | Whether version strings are detected at all for terminal, shell, editor, … |

## Platform support matrix

`✓` a real implementation is compiled in · `✗` only a `*_nosupport.c` stub, the module always
reports *Not supported on this platform* · `—` the module has no detection layer and works
everywhere.

A `✓` on every platform usually means the implementation is platform independent and lives in the
shared source list rather than a platform block — that is the case for `Command`, `PublicIp`,
`Weather`, `Zpool` and `TerminalTheme`.

The table says what is *compiled*. A `✓` can still fail at runtime because a device is absent, an
external library is missing (Zpool needs libzfs, Weather/PublicIp want network access), a display
server is not reachable (Display, DE, WM, …) or privileges are required (TPM, some sensors).

| Module | Linux | Android | FreeBSD | NetBSD | OpenBSD | Solaris | Haiku | GNU/Hurd | macOS | Windows |
|---|---|---|---|---|---|---|---|---|---|---|
| Title | — | — | — | — | — | — | — | — | — | — |
| Separator | — | — | — | — | — | — | — | — | — | — |
| OS | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Host | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✓ |
| BIOS | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✓ |
| Bootmgr | ✓ | ✗ | ✓ | ✓ | ✓ | ✗ | ✓ | ✗ | ✓ | ✓ |
| Board | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✓ |
| Chassis | ✓ | ✗ | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✓ |
| Kernel | — | — | — | — | — | — | — | — | — | — |
| InitSystem | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Uptime | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Loadavg | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Processes | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Packages | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Shell | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Editor | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Display | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Brightness | ✓ | ✗ | ✓ | ✓ | ✓ | ✗ | ✓ | ✗ | ✓ | ✓ |
| Monitor | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| LM | ✓ | ✗ | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✓ | ✓ |
| DE | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| WM | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| WMTheme | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Theme | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✓ | ✓ |
| Icons | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✗ | ✓ |
| Font | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Cursor | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✓ | ✓ |
| Wallpaper | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Terminal | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| TerminalFont | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| TerminalSize | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| TerminalTheme | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| CPU | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| CPUCache | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✓ |
| CPUUsage | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| GPU | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Top | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Codec | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Memory | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| PhysicalMemory | ✓ | ✗ | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✓ |
| Swap | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Disk | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Btrfs | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ |
| Zpool | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Battery | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✓ |
| PowerAdapter | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✓ | ✗ |
| Player | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Media | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| PublicIp | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| LocalIp | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| DNS | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Wifi | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✓ |
| DateTime | — | — | — | — | — | — | — | — | — | — |
| Locale | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Vulkan | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| OpenGL | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| OpenCL | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Users | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Bluetooth | ✓ | ✗ | ✓ | ✓ | ✗ | ✗ | ✓ | ✓ | ✓ | ✓ |
| BluetoothRadio | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ | ✓ | ✓ | ✓ |
| Sound | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Camera | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ | ✗ | ✗ | ✓ | ✓ |
| Gamepad | ✓ | ✗ | ✓ | ✗ | ✗ | ✗ | ✓ | ✗ | ✓ | ✓ |
| Mouse | ✓ | ✗ | ✓ | ✗ | ✗ | ✗ | ✓ | ✗ | ✓ | ✓ |
| Keyboard | ✓ | ✗ | ✓ | ✗ | ✗ | ✗ | ✓ | ✗ | ✓ | ✓ |
| Weather | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| NetIO | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✓ |
| DiskIO | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ | ✗ | ✓ | ✓ |
| PhysicalDisk | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✗ | ✓ | ✓ |
| TPM | ✓ | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ | ✓ | ✓ |
| Version | ✓ | ✓ | ✓ | ✗ | ✗ | ✗ | ✗ | ✓ | ✓ | ✓ |
| Break | — | — | — | — | — | — | — | — | — | — |
| Colors | — | — | — | — | — | — | — | — | — | — |
| Command | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Custom | — | — | — | — | — | — | — | — | — | — |
| Logo | — | — | — | — | — | — | — | — | — | — |

## Pitfalls that bite across modules

- **A typo in the config is silent.** `{ "type": "battery", "tempt": true }` is accepted; the
  unknown key is reported through `ffPrintError`, which does nothing unless
  `display.showErrors` is `true`. Turn it on while tuning a config.
- **Module command line flags are gone.** `fastfetch --battery-temp` fails with
  `Support of module options has been removed. Please add the flag to the JSON config instead.`
  Only the JSON config configures a module; `-s`/`--structure` only selects.
- **A wrong separator in `--structure` fails silently.** The list is split on `:` only —
  `-s os:colors`. With `-s os,colors` the whole string becomes one module name, no module matches,
  and because the "unknown type" message goes through `ffPrintError` the run exits 0 having printed
  nothing at all. If `-s` gives you an empty screen, check the separator first.
- **`custom` and `logo` only do anything in the object form.** The schema enumerates all 76 module
  names and the runtime accepts every one of them as a bare string, so `"custom"` is not rejected —
  but both modules are driven entirely by their options, and options only exist in the object form
  (`{ "type": "custom", "format": "hello" }`). A bare `"custom"` or `"logo"` runs and prints
  nothing.
- **"Not supported" and "no data" look different.** A missing implementation prints
  `Not supported on this platform`; an implementation that simply found nothing prints something
  like `No batteries found`. Both are errors, so both are hidden when `display.showErrors` is off.
- **A module that fails prints nothing by default**, which can silently change the line count of
  your output. Use `--stat` or enable `showErrors` when debugging.
- **`condition.system` uses the build target name, not the marketing name.** It is `macOS`, `SunOS`
  and `Windows`; GNU/Hurd reports `GNU`. The value comes from the `FF_SYSNAME` macro that
  `src/detection/version/version.c` defines per platform, and the schema's `systems` enum lists
  exactly those thirteen strings (`Unknown` included), so a schema-aware editor accepts every value
  the binary can actually report.
- **`percent` thresholds are ordered, not absolute.** The `green` threshold is "below this" and
  `yellow` is "between the two"; modules that measure *remaining* capacity (Battery, Disk free
  space) use inverted defaults, so the values do not mean the same thing in every module.
- **Temperature output is opt-in and platform dependent.** `temp` defaults to `false` almost
  everywhere because it costs an extra round trip (SMC, kstat, sysfs) or needs privileges.
- **Detection order matters inside a module.** Modules that must be measured before the terminal is
  redrawn (`CPUUsage`, `DiskIO`, `NetIO`, `Top`) are *prepared* first — see
  `prepareModuleJsonObject()` — and `waitTime` controls how long the sample window is.
- **Wide characters and alignment.** `display.key.width` aligns with a cursor jump
  (`ESC[<col>G`), which assumes the terminal agrees on the width of the logo and of any CJK text.
- **JSON mode is a different code path.** `--format json` calls `generateJsonResult()` instead of
  `printModule()`; a module may support one and not the other (`Logo` exists mainly for JSON). Some
  modules add fields that the console output does not show.
- **The config dialect depends on the file extension.** `.json` is strict; `.jsonc` adds comments and
  trailing commas; **`.json5` enables the full JSON5 reader**, which also accepts unquoted keys,
  single-quoted strings and extended escapes — including `\e`. A `format` containing `"\e[1m…"`
  therefore works in `config.json5` and is rejected in `config.jsonc` with
  `invalid escaped sequence in string`. When a config must work in both, use the format-string colour
  syntax (`{#bold_blue}…{#}`) or `\u001b`.
- **Platform support is build-time, not runtime.** Cross-compiling for another platform and running
  the same config can turn a `✓` into an error message with no other change.

## Known issues

Confirmed defects and deliberate quirks that are still present in the tree. The detailed report for
each — the exact config, the observed output and the file responsible — is in `bug.md` at the
repository root.

| Area | Symptom | Why it stays |
|---|---|---|
| `title` JSON | `userId` is a number on Unix and a string on Windows | The platform's native identifier is passed through as-is rather than normalised |
| `display`, `brightness` | Compact mode ignores `format` | Deliberate: compact mode renders its own single line |
| `brightness` | `ddcciSleep: -1` behaves exactly like `null` (skip detection) | No practical impact |
| `packages` | “No packages found” is an error in the console output but `{ "all": 0 }` in JSON | Deliberate: the console message stops the module from vanishing silently, and JSON is data |
| Schema enum ordering | `anyOf[0].enum` and `oneOf` are not in `src/modules/modules.c` order | Cosmetic — validation is unaffected |
| `cpucache` level gaps | A cache level with no entries ends the walk, so a later level is silently dropped from both the text and the JSON output | Not a fastfetch defect: a contiguous level list is a hardware invariant, so a gap means the kernel's sysfs is wrong. See B34 in `bug.md` for the reproduction |
| `gpu` format string | `{index}` is the printed position rather than the JSON `index` field, and `{core-count}` prints `-1` when the count is unknown | Deliberate: the format variable is defined as the position in the printed list, and `-1` is the raw unset sentinel rather than a formatted “unknown” |
| `bluetoothradio` format string | `{lmp-version}` and `{lmp-subversion}` print `-2147483648` when the platform reports nothing | Deliberate: the format engine counts every value `<= 0` as unset, so `{?lmp-version}…{?}` hides the block, while the JSON has `null` |
| `de` | `CDE`, `UKUI`, `LXDE` and `NebiDE` never get a version | No entry in the version probe; the display-server layer can still name them |
| `gamepad` on Windows | A DualShock 4 or Switch Pro shows no battery until another program has opened the controller once (starting Steam or DS4Windows and re-running works) | Open, root cause not settled — the module issues one bounded HID read and performs no handshake of its own; see B53 in `bug.md` |

Defects that were found while writing these pages and have since been fixed — the `command` /
`--structure` marker, the `condition.succeeded` desynchronisation, negative numeric options, the
`command.splitLines` counter and CRLF handling, the `logo` JSON `type` array, the `custom.key`
description, the schema's module / `systems` / `architectures` / `packages.disabled` enums,
`kernel`'s `{display-version}`, `display`'s `order` and compact-mode spacing, `editor`'s failure
reporting, the `media` cover-file leak, five spurious `percent` declarations, the duplicate
`deviceId` in the `vulkan` JSON, the missing `instanceVersion` in that JSON, `vulkan` reporting every
non-discrete device as `Integrated`, the `memory.*.used` mapping in the `opencl` and `vulkan` JSON,
`gpu`'s `hideType` filter in the JSON path, the `gpu` OpenGL memory sentinel, the `de` version
dispatch for GNOME Classic and Trinity, `datetime`'s `{day-in-year}` and `{hour-12}`, `colors`'
unvalidated `paddingLeft`, `block.width` and `block.range` elements, `bluetooth`'s single-device
numbering, the uninitialised `colorspace` in the macOS `camera` backend, the `dns` element that stayed uninitialised
on an unknown address family, `gamepad`'s missing `battery` JSON field and its leaked device strings,
and seven schema mismatches — the wrong
`display.color.key` / `display.keyWidth` paths in `$defs/keyColor` and `$defs/keyWidth`, the
`minLength: 1` on `$defs/key`, the missing `maximum` on `separator.times`, the `cpu`
`showPeCoreCount` default, the missing `cpucache.compact`, the missing `colors` bounds, and the two
x86-only `cpu` format variables — are recorded in `bug.md` and are not repeated here.

## Implementation

### Repository layout

```
src/
├── fastfetch.c               main(): two argv passes, config, run loop
├── flashfetch.c              a hard-coded neofetch-alike; demonstration only
├── modules/<name>/           one directory per module
│   ├── <name>.c              options, format args, print + JSON result
│   ├── <name>.h
│   └── option.h              the FFFooOptions struct
├── detection/<name>/         one directory per subsystem
│   ├── <name>.h              the result struct and the ffDetect* prototype
│   ├── <name>.c              shared helpers, if any
│   └── <name>_<platform>.c   exactly one per platform, *_nosupport.c included
├── common/                   printing, format strings, JSON helpers, options
│   ├── FFstrbuf.* FFlist.*   the two container types everything uses
│   ├── FFcache.*             the once-per-generation cache
│   ├── impl/                 the platform-independent implementations
│   └── windows/ android/ …   platform helpers (registry, binder, unicode, …)
├── options/                  global option groups (display, logo, general)
├── logo/                     built-in logos, plus image/ for the image backends
└── 3rdparty/                 vendored yyjson and sixel
presets/                      all.jsonc, neofetch.jsonc, … plus examples/ (one feature each)
tests/                        unit tests, one .c per subject, wired into CTest
doc/                          help.json and json_schema.json (both hand-written),
                              fastfetch.1.in (man page) and the design notes
completions/                  fastfetch.{bash,fish,zsh}
scripts/                      gen-man.py, gen-pciids.py, gen-amdgpuids.py
debian/                       packaging
CMakeLists.txt                the only build description; discovers src/modules/*/*.c
```

The four directories a change usually touches are `src/modules/`, `src/detection/`, `presets/` and
`doc/` — and a module page in this folder, which is derived from the first two.

### The module contract

A module is a `FFModuleBaseInfo` plus a small set of functions (see
`src/common/option.h` and `src/modules/battery/battery.c` for a complete example):

| Field / function | Purpose |
|---|---|
| `name`, `description` | Registry identity, used by `--list-modules` and matched case-insensitively |
| `displayName` | Localised name used in messages (`FF_MODULE_GET_DISPLAY_NAME(Battery)`) |
| `initOptions` / `destroyOptions` | Fill/tear down the option struct; the key icon default is set here |
| `parseJsonObject` | Read the module object; unknown keys call `ffPrintError` |
| `printModule` | Console output; returns `false` on failure |
| `generateJsonResult` | JSON output under `result`; `nullptr` means *Unsupported for JSON format* |
| `generateJsonConfig` | Serialise options back — powering `--gen-config` and the docs |
| `formatArgs` | The `{variable}` list; drives `-h <type>-format` and the JSON schema |
| `defaultOrder` | Sort key for generated configs; `0` means "leave out" |

Options are held in a fixed size buffer (`alignas(uint64_t) uint8_t optionBuf[FF_OPTION_MAX_SIZE]`)
and dispatched through `void*` function pointers — the code documents this as a conscious
"C polymorphism" shortcut. Every `option.h` therefore ends with a `static_assert` that the struct
fits into `FF_OPTION_MAX_SIZE`.

Options are parsed by helpers shared with the global settings, so both behave identically:
`ffJsonConfigParseModuleArgs` (the common `key`/`keyColor`/`keyIcon`/`keyWidth`/`outputColor`/`format`
keys), `ffPercentParseJsonObject`, `ffTempsParseJsonObject`, `ffJsonConfigParseEnum`,
`ffOptionParseBoolean`, …

One trap is worth knowing before writing a `generateJsonResult`: the document is serialised long
after the function returns, and yyjson does not always copy what you hand it.
`yyjson_mut_obj_add_str()` stores the pointer as-is whenever the string needs no escaping, so passing
a local buffer leaves the document holding freed memory — the failure shows up later as garbage or as
whatever reuses that allocation. Use `yyjson_mut_obj_add_strcpy()` for anything short-lived, or one
of the `add_strbuf` helpers, which copy.

### Output plumbing

Both output paths go through the same helpers (`src/common/impl/printing.c`):

- `ffPrintLogoAndKey()` prints the key block: icon (if `display.key.type` allows), the key text —
  or the custom `key` parsed as a format string with `{index}`, `{icon}`, `{module-name}` — the
  separator, and the colour for the value. A key of `" "` suppresses the whole block. If
  `moduleIndex > 0`, the module name is suffixed with the index (`Battery 2`).
- `ffPrintFormat()` parses `format` and prints *value*; on a parse error it reports
  `undefined format` through `ffPrintError()`.
- `ffPrintError()` is the single funnel for every failure. It returns immediately unless
  `display.showErrors` is set, so nothing about a module's failure is visible by default.

### Build system

- Module sources are **discovered**, not listed: `CMakeLists.txt` globs `src/modules/*/*.c`.
  The file name must equal the directory name, and a directory without a matching `.c` is skipped
  *silently* — a typo shows up as a missing module, never as an error. `--list-modules` is the
  quickest way to notice.
- Detection sources are **listed by hand**, once per platform block. Omitting a file does not fail
  on your machine; it fails with an `undefined reference` in the CI job for that platform.
- Each module has a build switch, `-DMODULE_DISABLE_<UPPERCASEDIR>=ON`, which sets
  `FF_MODULE_DISABLE_<DIR>` and removes the module from the registry (`src/modules/modules.c`).
  The code is still compiled; `LTO` is what drops it, so Release builds are the ones that shrink.
- The default module list is the `DEFAULT_STRUCTURE` cache variable in `CMakeLists.txt` and can be
  overridden at configure time by packagers.

### Adding a module — checklist

1. `src/modules/<name>/` with `<name>.c`, `<name>.h` and `option.h`; register the module in
   `src/modules/modules.c` (alphabetical group array) and the header in `src/modules/modules.h` and
   `src/modules/options.h`.
2. `src/detection/<name>/` with `<name>.h` plus one file per platform, including
   `<name>_nosupport.c`; add every file to the matching platform block in `CMakeLists.txt`.
3. Implement `ffInit<Name>Options` (default key icon!), `ffDestroy<Name>Options`,
   `ffParse<Name>JsonObject`, `ffPrint<Name>`, `ffGenerate<Name>JsonResult` and
   `ffGenerate<Name>JsonConfig`, then fill the `FFModuleBaseInfo` — including `formatArgs` and
   `defaultOrder`.
4. Add the new keys to the JSON schema documentation and the module's `Modules/` page.

### Verifying behaviour

```bash
fastfetch --list-modules                 # the registry
fastfetch -s battery                     # run one module
fastfetch -h battery-format              # its format variables
fastfetch --format json -s battery       # JSON structure
fastfetch --stat -s cpu:cpuusage:diskio  # per-module timing
fastfetch -h format-json | jq 'keys'     # every module's format help at once
```

`fastfetch --gen-config` writes to `~/.config/fastfetch/config.jsonc` and refuses to overwrite an
existing file — pass an explicit path (`fastfetch --gen-config /tmp/x.jsonc`) when experimenting,
otherwise you will find a fresh config shadowing your real one.

### Documentation sources of truth

| Artefact | Where it comes from | How to change it |
|---|---|---|
| `-h <type>-format` help | each module's `formatArgs` | nothing to do — it *is* the code |
| `doc/help.json` → `--help` | maintained by hand, read at **configure** time | edit the JSON, then re-run CMake: a plain rebuild will not pick it up |
| `$defs/<module>Format` in `doc/json_schema.json` | `fastfetch -h format-json`, which prints the block per module | re-run it and paste the blocks in |
| the rest of `doc/json_schema.json` | maintained by hand | edit it — and keep `doc/help.json` in step by hand, because nothing checks that the two agree |
| `Json-Schema` (wiki) | generated from `doc/json_schema.json` | `generate-schema-doc doc/json_schema.json --config template_name=md Json-Schema.md` |
| `Modules/*` (this folder) | `src/modules/*` and `src/detection/*` | by hand |

Two practical consequences. The schema is only as good as the hand-written half, so a schema that
disagrees with the binary is a schema bug — the runtime is the reference. And because `Json-Schema.md`
is generated and 1.4 MB, never edit it by hand: fix the source JSON and regenerate.

`--gen-config` is the third view of the same contract, and it is the one users actually see. It
serialises what the module *did* parse, so a key that appears there and is rejected by the runtime —
or the reverse — is a real inconsistency, not a documentation slip.

## Strengths and weaknesses

An honest reading of the design, so you can tell whether a limitation is a bug or a trade-off. Every
row below is visible from the pages in this folder.

### Strengths

| | |
|---|---|
| **Breadth** | 76 modules over ten platform families, each with a real backend or an explicit `*_nosupport.c` stub — "unsupported" is a runtime message, never a build failure |
| **Cost** | Written in C with no runtime dependency for the core; `--list-features` reports the optional accelerators (`threads`, `vulkan`, `chafa`, `libzfs`, Lua/QuickJS) that a build happens to have |
| **Machine-readable by default** | `--format json` emits one document covering every selected module, with the same field names the text path uses, so scripting needs no screen-scraping |
| **One contract, three views** | A module's `formatArgs` drives `-h <type>-format` *and* the schema's format block; `generateJsonConfig` drives `--gen-config`. Adding a module is mechanical because the registry, the help and the config generator all read the same declaration |
| **Config as first-class** | JSONC with a published JSON schema, `$schema`-driven editor completion, `--gen-config` to discover keys, and presets shipped in-tree (`neofetch`, `screenfetch`, `archey`, …) that make behaviour reproducible across machines |
| **Per-module formatting** | Any module's value can be re-rendered from named variables, with conditionals and colour syntax — the reason `Modules/*` documents format variables per module rather than as an afterthought |
| **Documented platform reality** | Where a backend cannot fill a field, the pages say so per platform instead of implying a uniform answer |

### Weaknesses

| | |
|---|---|
| **The CLI was deliberately trimmed** | Since 2.52.0 there are no per-module command line options, so a module can only be configured through a config file. Scripts written against older releases break, and there is no way to tweak one module for a single invocation |
| **Failures are silent by default** | `display.showErrors` defaults to `false`, so a module that fails simply does not appear. "Why is my module missing?" is invisible unless `--stat` is used or the option is turned on |
| **"Unknown" is spelled many ways** | Depending on the field it is `0`, `-1`, `-DBL_MAX` or an empty string, and the sentinel can reach a custom format — `{rx-rate}` prints `-1.7976931348623157e308`. The conditional syntax then behaves inconsistently, because a value counts as set only when it is `> 0` |
| **Format strings are rarely portable** | Status, security and protocol strings differ per platform (`up` vs `Connected` vs `Power On`), and the signal-quality numbers are percentages of different things. A format tuned on one OS is a guess on another |
| **Some modules are single-use per run** | A second `weather` module, or two `publicip` modules for the same address family, aborts the whole process with an exit status before anything is printed |
| **The built-in HTTP client has no TLS** | `weather` is fixed to the hard-coded `wttr.in`; `publicip` defaults to `ipinfo.io` but accepts any `http://` URL. Both reject `https` outright (`Error: only http: protocol is supported`), so anything that needs TLS means falling back to the `command` module with `curl` |
| **Documentation is maintained in parallel** | `doc/help.json`, `doc/json_schema.json` and this wiki are three hand-written views of the same options, and only the schema's format blocks are generated. Nothing checks that they agree, so drift is possible — the binary is the reference |
| **The two-pass design is a contributor trap** | Work done in the print pass that belongs in the prepare pass is duplicated rather than reported, and a module that returns `false` after printing successfully suppresses every later `condition.succeeded` module |
| **Text and JSON can disagree** | An empty interface list is an error in the text path and `"result": []` in JSON, and `datetime`'s JSON value is written from a second clock reading taken while the document is serialised, so it need not equal the text output |
| **One config format** | JSONC only. Comments and trailing commas are supported, TOML and YAML are not |

### What that means in practice

- For **scripting**, prefer `--format json` and treat the text output as human-facing only.
- For **a one-off**, expect to write a small config file rather than a long command line.
- For **a portable config**, test the format strings on each target platform; the per-module pages
  list exactly which variables are safe.
- For **diagnosing a missing line**, turn on `display.showErrors` first — the default hides the
  answer.
- For **extending**, read `### The module contract` above and keep new work in the prepare pass if it
  must happen before the modules run.

## Conventions used by the pages in this folder

Each page is written for users first and developers last, and always in this order:

1. **Header** — one-line description taken from the module registry, a small metadata table
   (`Module type`, `Default order`, `Module source`, `Detection source`) and a
   sample of the default output.
2. **Platform support** — what each platform actually implements, and what is missing.
3. **Configuration** — the module's JSON keys with defaults, then the format string variables, the
   JSON output shape and a couple of copy-pasteable examples.
4. **Pitfalls** — what surprises users, including the platform-specific ones.
5. **Implementation** — how the data is obtained, per platform, including the syscalls, APIs,
   parsers and known limitations.

Everything is derived from the source tree at the version in development (2.69.0 at the time of
writing); when a page and the code disagree, the code wins — and the page is the bug.
