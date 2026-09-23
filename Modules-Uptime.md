# Uptime

> Print how long the system has been running

| | |
|---|---|
| Module type | `uptime` |
| Default order | 11 (only used by `--gen-config`) |
| Module source | `src/modules/uptime/uptime.c` |
| Detection source | `src/detection/uptime/` |

Prints the time elapsed since the machine booted, as a single human-readable duration. The
duration is rounded to the nearest second and then to the nearest minute, and only the three
largest non-zero units are kept, so a machine that has been up for 46 minutes prints minutes
alone.

```
Uptime: 42 seconds
Uptime: 46 mins
Uptime: 1 hour, 4 mins
Uptime: 3 days, 2 hours, 5 mins
```

The default output is exactly the `{formatted}` value (see *Format string*), and it is affected
by the global `display.duration` settings.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `uptime_linux.c` | `/proc/uptime`, with a `clock_gettime(CLOCK_BOOTTIME)` fallback |
| Android | `uptime_linux.c` | `/proc/uptime` is not readable by apps; always uses `CLOCK_BOOTTIME` |
| FreeBSD / NetBSD / OpenBSD / DragonFly | `uptime_bsd.c` | `sysctl({CTL_KERN, KERN_BOOTTIME})` |
| macOS | `uptime_bsd.c` | Same `KERN_BOOTTIME` reader |
| Solaris / illumos | `uptime_sunos.c` | Reads the `BOOT_TIME` record from the utmpx database |
| Haiku | `uptime_haiku.c` | `system_time()` |
| GNU/Hurd | `uptime_linux.c` | `/proc/uptime` only — there is no `CLOCK_BOOTTIME` fallback |
| Windows | `uptime_windows.c` | `SharedUserData->InterruptTime` |

`src/detection/uptime/uptime_nosupport.c` does not exist; every platform has a real
implementation.

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

There are **no module-specific keys**. The shape of the duration text is controlled by two
global settings instead:

```jsonc
{
    "display": {
        "duration": {
            // true: "3d 2h 5m"   false (default): "3 days, 2 hours, 5 mins"
            "abbreviation": false,
            // "default" and "always" insert a space before the unit, "never" does not
            "spaceBeforeUnit": "default"
        }
    },
    "modules": [{ "type": "uptime" }]
}
```

## Format string

Run `fastfetch -h uptime-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{days}` | Whole days after boot |
| `{hours}` | Hours after boot (0–23) |
| `{minutes}` | Minutes after boot (0–59) |
| `{seconds}` | Seconds after boot (0–59) |
| `{milliseconds}` | Milliseconds after boot (0–999) |
| `{boot-time}` | Boot time in local timezone, `YYYY-MM-DD hh:mm:ss` |
| `{years}` | Whole years after boot |
| `{days-of-year}` | Days after the last whole-year anniversary |
| `{years-fraction}` | Years as a decimal number |
| `{formatted}` | The formatted uptime string used by the default output |

The first five variables are pure arithmetic on the raw millisecond count and are **not**
rounded. `{years}`, `{days-of-year}` and `{years-fraction}` are calendar-based: they compare the
boot timestamp against now in local time, so they account for leap years and are always `0` for
a machine that has been up for less than a day.

## JSON output

```jsonc
{
    "type": "Uptime",
    "result": {
        "uptime": 2782669,
        "bootTime": "2026-09-22T09:00:50.809+0800"
    }
}
```

`uptime` is in milliseconds. `bootTime` is an ISO 8601 string with milliseconds and a numeric
timezone offset — a different format from the `{boot-time}` variable.

## Examples

```jsonc
// Only the raw counter, no rounding and no unit names
{ "type": "uptime", "format": "{days}d {hours}h {minutes}m {seconds}s" }
```

```jsonc
// Show when the machine came up instead of how long it has been up
{ "type": "uptime", "key": "Booted", "format": "{boot-time}" }
```

## Pitfalls

- **The default text is double-rounded.** `ffDurationAppendNum()` receives
  `(uptime + 500) / 1000` and then rounds again: seconds below 30 are dropped, 30 and above bump
  the minute. `46 mins` therefore covers anything from 45 min 30 s to 46 min 29 s, and the value
  can disagree with `{minutes}` by one.
- **Seconds are never printed by the default output once the total passes a minute.** The
  formatter keeps at most three units (days, hours, minutes) and has no branch that appends
  seconds for a longer uptime. Use `{seconds}` or `{formatted}` plus a custom `format` if you
  want them.
- **An uptime of 100 days or more gets a `(!)` suffix** appended to the day unit
  (`101 days(!), 3 hours`) as a hint that the number is probably wrong.
- **`{boot-time}` is derived, not read, on most platforms.** Only the BSDs, macOS and Solaris
  read a boot timestamp from the kernel; Linux, Windows, Haiku and Android compute
  `bootTime = now - uptime`. A clock adjustment or NTP step therefore moves the reported boot
  time, and `bootTime` can briefly disagree with `uptime` by the size of the step.
- **GNU/Hurd has no fallback.** Its `/proc/uptime` is the only source, and the module reports
  `read(/proc/uptime) failed` when it cannot be read. Every other Linux-like platform retries
  with `CLOCK_BOOTTIME`.
- **`{boot-time}` and the JSON `bootTime` use different formats** (`YYYY-MM-DD hh:mm:ss` versus
  ISO 8601 with milliseconds and offset). A script that parses one will not parse the other.
- **The rounding is done with `+500` on a `uint64_t`**, so an uptime above roughly 5.8×10^11
  hours would wrap; not reachable in practice, but it is why the arithmetic is not a plain
  division.

## Implementation

### Linux, Android and GNU/Hurd

`ffDetectUptime()` tries `/proc/uptime` first and parses the first whitespace-delimited number
with `strtod()`, converting seconds to milliseconds. The read is capped at 63 bytes. The
Android and GNU/Hurd builds then diverge:

- **Android** skips the `/proc/uptime` branch entirely (the comment cites
  `cat: /proc/uptime: Permission denied`) and uses `clock_gettime(CLOCK_BOOTTIME)`, which is
  also what a Linux build falls back to when `/proc/uptime` is unreadable — the issue reference
  is #620, container compatibility.
- **GNU/Hurd** has no `CLOCK_BOOTTIME`, so a failed `/proc/uptime` read is terminal and returns
  the error string `read(/proc/uptime) failed`.

### FreeBSD, NetBSD, OpenBSD, DragonFly and macOS

`sysctl({CTL_KERN, KERN_BOOTTIME})` fills a `struct timeval` (a `struct timespec` on NetBSD),
which becomes `bootTime`; `uptime` is then `now - bootTime`.

### Solaris

The utmpx database is walked with `setutxent()`/`getutxent()` and the first entry whose
`ut_type` is `BOOT_TIME` supplies the boot timestamp. `endutxent()` closes the iteration. If no
`BOOT_TIME` record exists, both fields stay `0` and the module prints `0 seconds` rather than an
error.

### Haiku

`system_time()` returns microseconds since boot, which is converted to milliseconds. There is no
boot timestamp in the result, so `bootTime` is again derived from the current time.

### Windows

`SharedUserData->InterruptTime` is read directly from the user-shared page — no syscall and no
WMI. The field counts 100-nanosecond intervals since boot, so the conversion to milliseconds is
a division by 10000. The source comment notes that
`NtQuerySystemInformation(SystemTimeOfDayInformation)` reports the boot time directly and
matches what WMI's `Win32_OperatingSystem` returns, but with only 0.5-second accuracy, which is
why it is not used.

### Shared

`FFUptimeResult` carries both `bootTime` and `uptime` in milliseconds, so the platform readers
either compute one from the other or fill both. `ffPrintUptime()` and
`ffGenerateUptimeJsonResult()` each call `ffDetectUptime()` independently, but a run uses only
one of them: `--format json` replaces the printed line with the JSON document rather than
producing both.
