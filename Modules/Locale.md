# Locale

> Print system locale name

| | |
|---|---|
| Module type | `locale` |
| Default order | 54 (only used by `--gen-config`) |
| Module source | `src/modules/locale/locale.c` |
| Detection source | `src/detection/locale/` |

Prints the locale the program is running under, as a single string. There is no key suffix, no
percentage and no list — every platform reports exactly one value.

```
Locale: en_US.UTF-8
```

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `locale_linux.c` | `LC_ALL`, then `LANG`, then `setlocale(LC_TIME, nullptr)` |
| Android | `locale_linux.c` | Same |
| FreeBSD / MidnightBSD / DragonFly | `locale_linux.c` | Same |
| NetBSD | `locale_linux.c` | Same |
| OpenBSD | `locale_linux.c` | Same |
| Solaris / illumos | `locale_linux.c` | Same |
| Haiku | `locale_linux.c` | Same |
| GNU/Hurd | `locale_linux.c` | Same |
| macOS | `locale_linux.c` | Same |
| Windows | `locale_windows.c` | The two variables, then `GetUserDefaultLocaleName()` plus a code page |

Despite its name, `locale_linux.c` is the POSIX implementation and is used on every platform except
Windows.

## Configuration

The module has no options of its own.

| Key | Type | Default | Description |
|---|---|---|---|
| `key` | string | `Locale` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

## Format string

Run `fastfetch -h locale-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{result}` | The locale string, exactly as it is printed |

`{result}` is not available in the key format.

## JSON output

```jsonc
[
    {
        "type": "Locale",
        "result": "en_US.UTF-8"
    }
]
```

The result of this module is a plain string rather than an object or an array, and it is the only field.
On failure the module writes `{ "type": "Locale", "error": "…" }` instead — `Failed to detect locale` on
POSIX and `GetUserDefaultLocaleName() failed` on Windows.

## Examples

```jsonc
// Only the language part of the locale
{ "type": "locale", "format": "{result:5}" }
```

```jsonc
// A locale-independent line: the key is the only thing that changes with the language
{ "type": "locale", "key": "lang", "format": "{result}" }
```

## Pitfalls

- **A set `LC_ALL` or `LANG` wins over the system locale.** Both variables are checked before the
  platform API, so `LC_ALL=de_DE.UTF-8 fastfetch` reports `de_DE.UTF-8` on an en-US machine, and a
  container that exports `LANG=C` reports `C`. The value is copied verbatim — nothing is normalised,
  validated or expanded, so `LANG=POSIX` prints `POSIX` and a value with a typo is reported as-is.
- **Only `LC_ALL` and `LANG` are read.** The category-specific variables (`LC_MESSAGES`, `LC_CTYPE`,
  `LC_TIME`, `LC_NUMERIC`, …) are never consulted, even though POSIX would let `LC_MESSAGES` override
  `LANG` for message formatting. Setting `LC_MESSAGES=ja_JP.UTF-8` alone changes nothing in the output.
- **The POSIX fallback is the *time* category.** `setlocale(LC_TIME, nullptr)` does not report "the
  system locale" — it reports whatever this program has activated for `LC_TIME`. fastfetch itself calls
  `setlocale(LC_TIME, "")` at start-up, so the fallback ends up being the environment-derived time
  locale, which is why a bare `LANG=fr_FR.UTF-8` shows up even when neither variable is re-read here.
- **On Windows the value is synthesised from two sources.** `GetUserDefaultLocaleName()` provides the
  language and region, and the code page of the console *as it was when fastfetch started*
  (`instance.state.platform.initCP`) is appended after a dot: `65001` → `UTF-8`, `936` → `GBK`,
  `54936` → `GB18030`, `950` → `BIG5`, `932` → `Shift_JIS`, `949` → `EUC-KR`, `20932` → `EUC-JP`,
  `20866` → `KOI8-R`, `21866` → `KOI8-U`, `20127` → `US-ASCII`, `874` and `1250`–`1258` → `Windows-<n>`,
  anything else → `CP<n>`. There is no code page suffix at all when that value is `0`.
- **On Windows only the first hyphen is rewritten.** Windows reports `en-US` where POSIX writes
  `en_US`, so the module replaces the character at index 2 when it is `-`. That normalises
  `language-REGION` but leaves a script subtag alone: `zh-Hans-CN` becomes `zh_Hans-CN`, and
  `sr-Latn-RS` stays partly hyphenated.
- **The `No locale found` error of the JSON path is unreachable.** The POSIX backends return
  `Failed to detect locale` as soon as all three sources are empty, and the Windows backend returns
  `GetUserDefaultLocaleName() failed` before the empty check; the module's own
  `if (locale.length == 0)` guard therefore never fires. The text path has no such guard at all — it
  would print an empty value after the key.

## Implementation

`ffDetectLocale(FFstrbuf* result)` is the only detection entry point. It appends its findings to the
buffer it is handed and returns an error string; `ffPrintLocale()` and `ffGenerateLocaleJsonResult()`
each call it themselves, so a `--dynamic-interval` run re-reads the environment and the platform API on
every round.

The two backends share the same shape — `LC_ALL`, then `LANG`, then one platform call — and differ only
in that last step:

### POSIX (`locale_linux.c`)

The third source is `setlocale(LC_TIME, nullptr)`, and the function reports
`Failed to detect locale` when nothing was found. Empty values are skipped rather than accepted, so
`LC_ALL=` (set but empty) falls through to `LANG`.

### Windows (`locale_windows.c`)

The third source is `GetUserDefaultLocaleName()`. A return value of `0` or `1` (the latter would mean a
single NUL, i.e. an empty name) is reported as `GetUserDefaultLocaleName() failed`. The name is appended
without its terminator, its second character is normalised from `-` to `_`, and the code page is
appended as described above. `LANG` is still consulted before the API because MSYS2 and Cygwin shells
set it.
