# DateTime

> Print the current date and time

| | |
|---|---|
| Module type | `datetime` |
| Default order | 53 — only used by `--gen-config` |
| Module source | `src/modules/datetime/datetime.c` |
| Detection source | — (`src/common/time.h` for the clock) |

The registry name is `DateTime` — that is what `--list-modules` and this page's file name use, and what
a config's `type` has to say. The key it *prints* is the localised display name, `Date & Time` in
English.

Prints the local date and time at the moment the module runs:

```
Date & Time: 2026-01-01 09:00:00
```

That is ISO 8601 with a space instead of `T`, produced by `strftime("%F %T")`. The module reads the
wall clock directly — there is no caching layer involved, so with `--dynamic-interval` each round
prints a fresh timestamp.

With a `format` you can build any shape you like from 24 variables:

```jsonc
{ "type": "datetime", "format": "{year}-{month-pretty}-{day-pretty} {hour-pretty}:{minute-pretty}:{second-pretty} {offset-from-utc}" }
```

```
Date & Time: 2026-01-01 09:00:00 +0800
```

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Everywhere | `src/modules/datetime/datetime.c` | Platform independent — no detection layer |

The clock comes from `ffTimeGetNow()`. Only the *implementation* of that helper varies:
`clock_gettime(CLOCK_REALTIME)` on POSIX, the shared user-data `KSYSTEM_TIME` on Windows,
`real_time_clock_usecs()` on Haiku. Everything downstream is standard C `localtime()` + `strftime()`,
so the output is identical on every platform.

## Configuration

| Option | Type | Default | Description |
|---|---|---|---|
| `format` | string | – | The output format; empty means the default `YYYY-MM-DD HH:MM:SS` |
| `key` | string | – | Replaces `Date & Time`; a single space hides the key and the separator |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | – | Used when `display.key.type` includes the icon bit |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `condition` | object | – | See [Global](Modules-Global#condition-in-practice) |

There is no timezone, no locale and no clock-source option: the module uses the process's local time
and the process's `LC_TIME`. An empty `format` string (`""` or `null`) falls back to the default
output rather than printing an empty line, because the module only takes the format path when the
string is non-empty.

## Format string

`fastfetch -h datetime-format` prints the list, and the module is one of the few whose `-h` output is
architecture-independent. The example column below is the timestamp the source file itself uses as
its reference — 2022-02-21, a Monday, local time 15:18:37:
| Variable | Description | For 2022-02-21 15:18:37 |
|---|---|---|
| `{year}` | Year | `2022` |
| `{year-short}` | Last two digits of year | `22` |
| `{month}` | Month | `2` |
| `{month-pretty}` | Month with leading zero | `02` |
| `{month-name}` | Month name | `February` |
| `{month-name-short}` | Month name short | `Feb` |
| `{week}` | Week number on year | `8` |
| `{weekday}` | Weekday | `Monday` |
| `{weekday-short}` | Weekday short | `Mon` |
| `{day-in-year}` | Day in year | `52` |
| `{day-in-month}` | Day in month | `21` |
| `{day-in-week}` | Day in week | `1` |
| `{day-pretty}` | Day in month with leading zero | `21` |
| `{hour}` | Hour | `15` |
| `{hour-pretty}` | Hour with leading zero | `15` |
| `{hour-12}` | Hour 12h format | `3` |
| `{hour-12-pretty}` | Hour 12h format with leading zero | `03` |
| `{minute}` | Minute | `18` |
| `{minute-pretty}` | Minute with leading zero | `18` |
| `{second}` | Second | `37` |
| `{second-pretty}` | Second with leading zero | `37` |
| `{am-pm}` | AM or PM | `PM` |
| `{offset-from-utc}` | Offset from UTC in the ISO 8601 format | depends on the timezone, e.g. `+0800` |
| `{timezone-name}` | Locale-dependent timezone name or abbreviation | depends on the timezone, e.g. `CST` |

All 24 are output-format only; none of them is available in the key. The `*` markers that `-h` prints
for some modules do not appear here.

## JSON output

```jsonc
{ "type": "DateTime", "result": "2026-01-01T09:00:00.123+0800" }
```

The `result` is a full ISO 8601 timestamp with milliseconds and a numeric UTC offset, produced by
`ffTimeToFullStr()` — **not** the module's text output and **not** affected by `format`. The value is
sampled when the JSON document is written, which is a separate `ffTimeGetNow()` call from the one the
text output used, so the two can differ by a few milliseconds (or, in `--format json` runs, be the
same instant only by luck).

## Examples

European day-first format:

```jsonc
{ "type": "datetime", "format": "{day-pretty}.{month-pretty}.{year}" }
```

A 12-hour clock with an explicit zone:

```jsonc
{ "type": "datetime", "format": "{hour-12}:{minute-pretty}:{second-pretty} {am-pm} ({timezone-name})" }
```

Week and day-of-year, for a log header:

```jsonc
{ "type": "datetime", "format": "week {week}, day {day-in-year} of {year}" }
```

A timestamp that is part of a sentence, with a renamed key:

```jsonc
{ "type": "datetime", "key": "Now", "format": "{weekday}, {month-name} {day-in-month}" }
```

```
Now: Monday, February 21
```

Dropping the key entirely:

```jsonc
{ "type": "datetime", "key": " " }
```

## Pitfalls

- **`{week}` is not an ISO 8601 week number.** It is `tm_yday / 7 + 1`, i.e. a week counter that
  starts on 1 January and ignores both the weekday and the year boundary. On 2026-09-22 it prints
  `38` where `date +%V` (and every ISO calendar) says `39`.
- **`{day-in-week}` numbers Monday as 1 and Sunday as 7**, which differs from C's `tm_wday`
  (Sunday 0) and from the `%u`/`%w` distinction that trips people up elsewhere.
- **Month and weekday names follow `LC_TIME`.** The module never calls `setlocale(LC_ALL, "")`, but
  `src/common/impl/init.c` does call `setlocale(LC_TIME, "")`, so `{month-name}`, `{month-name-short}`,
  `{weekday}`, `{weekday-short}` and `{am-pm}` are localised — with `LC_TIME=de_DE.UTF-8` the same run
  prints `Dienstag` where the C locale prints `Tuesday`. Note that only `LC_TIME` is consulted;
  `LANG` alone has no effect unless it selects the time category.
- **An unknown variable is printed literally and silently.** `format: "{nope}"` outputs `{nope}` with
  no warning, even with `display.showErrors` enabled, so a typo in a long format string is easy to
  miss.
- **Only `{{` is an escape.** `{{` renders a literal `{`; `}` and `}}` are passed through as-is, so
  `format: "{{year}}"` prints `{year}}` rather than `{year}`. There is no way to write `{` … `}`
  around a variable without the stray closing brace.
- **An unknown key is reported *before* the timestamp.** The parse pass runs first, so
  `{ "type": "datetime", "nope": 1 }` prints

  ```
  Date & Time: Unknown JSON key nope
  Date & Time: 2022-02-21 15:18:37
  ```

  The warning only shows up when `display.showErrors` is true.
- **`format` does not change the JSON output.** `--format json` always reports the full ISO
  timestamp in `result`, so a config that formats the text output will disagree with the JSON. If a
  consumer needs the formatted string, read the text output or use a `command` module.
- **The module has no fixed granularity option.** `{second}` is as fine as it gets; there is no
  sub-second variable, and `result` in JSON is the only field that carries milliseconds.

## Implementation

`ffPrintDateTime()` (`src/modules/datetime/datetime.c`) takes the clock in milliseconds, converts it
to `time_t` and runs `localtime()`. From there it takes one of two paths:

- **No `format`**: a single `strftime(buffer, sizeof buffer, "%F %T", tm)` — the ISO date, a space,
  the 24-hour time — written after the key with `puts()`. A `strftime()` failure (buffer too small,
  which cannot happen for this pattern) reports `strftime() failed` and returns `false`.
- **With `format`**: `printDateTimeFormat()` fills an `FFDateTimeResult` on the stack field by field
  and hands all 24 values to `FF_PRINT_FORMAT_CHECKED`.

The provenance of the fields is mixed, which is worth knowing when one of them looks off: `{year}`,
`{month}`, `{hour}`, `{minute}`, `{second}` and the derived `{year-short}`, `{week}`,
`{day-in-year}`, `{day-in-week}` are arithmetic on `struct tm`, while `{month-pretty}`,
`{month-name}`, `{month-name-short}`, `{weekday}`, `{weekday-short}`, `{day-pretty}`,
`{hour-pretty}`, `{hour-12-pretty}`, `{minute-pretty}`, `{second-pretty}`, `{offset-from-utc}`,
`{timezone-name}` and `{am-pm}` are `strftime()` output. The one place where the two families could
have disagreed — the 12-hour clock, where `strftime("%I")` maps midnight and noon to `12` while
`tm_hour % 12` maps them to `0` — is reconciled with `result.hour12 = result.hour % 12 ?: 12`: the
GNU `?:` extension, used throughout the codebase, yields the right-hand side when the remainder is
zero.

`ffGenerateDateTimeJsonResult()` does not touch the options at all: it writes `ffTimeToFullStr(
ffTimeGetNow())` into `result`, using `yyjson_mut_obj_add_strcpy()` so the static buffer the helper
returns is copied rather than aliased.
