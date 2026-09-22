# Users

> Print users who are currently logged in

| | |
|---|---|
| Module type | `users` |
| Default order | 58 (only used by `--gen-config`) |
| Module source | `src/modules/users/users.c` |
| Detection source | `src/detection/users/` |

Prints one line per logged-in user: the name, the host it came from when it is a remote session,
and the login time.

```
Users: user - login time 2026-09-22 18:02:12
Users: user@my-pc - login time 2026-09-22 09:15:03
```

With more than one user the key is numbered (`Users 2:`, `Users 3:` …) and each user gets its own
line. The `compact` option collapses everything into a single comma-separated list of names.

```
Users: user, admin, guest
```

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `users_linux.c` | `utmpx` first, `/run/systemd/users/` when utmp yields nothing |
| Android | `users_linux.c` | Same file, using the non-`utmpx` fallback headers |
| FreeBSD / NetBSD / DragonFly | `users_linux.c` | Same file |
| Solaris / illumos | `users_linux.c` | Same file |
| Haiku | `users_linux.c` | Same file |
| GNU/Hurd | `users_linux.c` | Same file |
| macOS | `users_linux.c` | Same file |
| OpenBSD | `users_obsd.c` | Reads `_PATH_UTMP` directly with `fread()`, no `utmpx` API |
| Windows | `users_windows.c` | `WTSEnumerateSessionsExW()` |

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `compact` | boolean | `false` | Print all active users on one line, names only |
| `myselfOnly` | boolean | `false` | Show only the current user |
| `key` | string | `Users` | Module key. A single space hides the key and the separator |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

`myselfOnly` compares against the platform's own user name (from the `Title` module's detection),
case-sensitively on Unix.

## Format string

Run `fastfetch -h users-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{name}` | User name |
| `{host-name}` | Host name |
| `{session-name}` | Session name |
| `{client-ip}` | Client IP |
| `{login-time}` | Login time, in the local timezone |
| `{days}` | Whole days since login |
| `{hours}` | Hours component since login |
| `{minutes}` | Minutes component since login |
| `{seconds}` | Seconds component since login |
| `{milliseconds}` | Milliseconds component since login |
| `{years}` | Whole years since login |
| `{days-of-year}` | Days since login, excluding whole years |
| `{years-fraction}` | Years since login, as a fraction |

None of the thirteen is marked `*` in the help output, so none is available in the key format.

The five duration components are a **breakdown**, not totals: `{days} {hours}:{minutes}:{seconds}`
is the elapsed time, and `{milliseconds}` is the sub-second remainder. `{years}` / `{days-of-year}`
/ `{years-fraction}` come from a separate calendar-aware calculation, so `{years-fraction}` is not
`{days} / 365`. A custom format is printed once per user, and the same key-numbering rule applies:

```jsonc
{ "type": "users", "format": "{name}|{host-name}|{session-name}|{client-ip}|{login-time}|{days}|{hours}|{minutes}|{seconds}|{milliseconds}|{years}|{days-of-year}|{years-fraction}" }
```

```
Users: user||Console||2026-09-22 18:02:12|0|2|43|53|926|0|0|0.00
```

## JSON output

```jsonc
[
    {
        "type": "Users",
        "result": [
            {
                "name": "user",
                "hostName": "",
                "sessionName": "Console",
                "clientIp": "",
                "loginTime": "2026-09-22T18:02:12.788+0800"
            }
        ]
    }
]
```

- `result` is an **array** with one object per user, and the five keys are always present.
- `loginTime` is a full ISO-8601 string with milliseconds and the UTC offset, **not** the short
  form the text output prints. It is `null` when the login time is unknown.
- `hostName`, `sessionName` and `clientIp` are `""` rather than `null` when they do not apply.
- The duration fields have no JSON counterpart at all — they are computed in the print function
  from the current time, so they would be stale the moment they were written.
- On failure the object carries `error` instead and has no `result`.

## Examples

```jsonc
// Just the names, for a shell prompt
{ "type": "users", "format": "{name}" }
```

```jsonc
// Session overview for remote machines
{ "type": "users", "format": "{name}@{host-name} from {client-ip} on {session-name}" }
```

```jsonc
// One line, names only
{ "type": "users", "compact": true }
```

## Pitfalls

- **`compact` is ignored as soon as a `format` is set.** It only changes the default output; with
  `format` the module always prints one line per user.
- **The text path fails on an empty list, the JSON path does not.** With no logged-in user the
  default output reports `Unable to detect any users` (silently, unless `display.showErrors` is
  `true`), while `--format json` emits `{"result": []}` — the same divergence `keyboard`, `mouse`,
  `sound` and `poweradapter` have.
- **`loginTime` uses two different formats in one module.** The default line and `{login-time}`
  print `ffTimeToShortStr()` (`2026-09-22 18:02:12`); the JSON prints `ffTimeToFullStr()`
  (`2026-09-22T18:02:12.788+0800`). A script that parses both has to handle both.
- **A missing login time produces an absurd duration.** The duration fields are computed as
  `now - loginTime`, so a backend that could not read the time leaves `loginTime` at `0` and the
  elapsed values become "everything since 1970". On Windows this happens whenever the
  `WTSSessionInfo` query fails, and on OpenBSD / in the utmp path whenever the record has no
  timestamp.
- **The two Unix sources disagree about the host name.** The utmp path copies `ut_host` verbatim
  (often empty for a local session), while the systemd path uses `REMOTE_HOST` with `[` and `]`
  stripped and substitutes the literal `localhost` when the session is local. The session name is
  the tty when there is one and the systemd service name otherwise.
- **The systemd path only runs when utmp produced nothing.** On Linux the utmp/utmpx pass runs
  first and `/run/systemd/users/` is consulted only if the resulting list is empty — that is the
  workaround for distributions that stopped updating `/var/run/utmp` (Debian/Ubuntu, upstream
  issue #2064). A stale utmp entry therefore hides the systemd answer.
- **The systemd backend parses private, unstable files.** `detectUserBySystemd()` reads
  `/run/systemd/users/<uid>` and `/run/systemd/sessions/<id>` for `NAME`, `STATE`, `REALTIME`,
  `ONLINE_SESSIONS`, `REMOTE_HOST`, `TTY` and `SERVICE`; the source itself carries the warning
  "This is private data. Do not parse". A systemd change to those files breaks this silently.
- **Only `USER_PROCESS` records are counted on Unix.** Login records, boot records and dead
  processes in the utmp file are skipped, and a user appearing in several records keeps only the
  **newest** login time — one entry per user name, not one per session.
- **`clientIp` only exists on Linux, GNU/Hurd and Windows.** `fillUtmpIpAddr()` is a no-op
  everywhere else, and even on Linux it needs `ut_addr_v6` to be populated, which only happens for
  network logins. The Windows value comes from `WTSClientAddress`, which is meaningful for RDP
  sessions and usually `0.0.0.0` for a local console.
- **On Windows only `WTSActive` sessions are listed.** Disconnected and listening sessions are
  skipped, so a machine with a disconnected RDP session shows fewer users than Task Manager does.
- **`myselfOnly` compares names, not identities.** On Unix it is a case-sensitive string compare
  against the detected user name, and on Windows a case-sensitive compare against the platform
  user name, so a domain-qualified login can fail to match the short name.
- **Errors are invisible by default.** `Unable to detect any users`, `WTSEnumerateSessionsW(…) failed`
  and `opendir("/run/systemd/users/") failed` all go through `ffPrintError()` and need
  `display.showErrors` to be `true`.

## Implementation

`ffDetectUsers()` fills an `FFlist` of `FFUserResult` (`name`, `hostName`, `sessionName`,
`clientIp`, `loginTime` as a Unix millisecond timestamp) and returns an error string or `nullptr`.
Both the print and the JSON function destroy the four strbufs of every element afterwards; nothing
is cached, so the list is rebuilt on every call.

`ffPrintUsers()` renders either the compact list (names joined with `, `), the default per-user
line (`name[@host] - login time <short>`), or one formatted line per user with the five duration
components and the three calendar-age values computed up front.

### Linux / BSD / macOS

`detectByUtmp()` walks `getutxent()`, keeping `USER_PROCESS` entries and deduplicating by name
(newest login wins). On Linux and GNU/Hurd the IPv4/IPv6 address is derived from `ut_addr_v6` with
`inet_ntop()`. If the list is still empty on Linux, `detectBySystemd()` enumerates
`/run/systemd/users/` (or reads the single `<uid>` file with `myselfOnly`) and follows each
`ONLINE_SESSIONS` entry into `/run/systemd/sessions/`, ignoring sessions whose `SERVICE` is
`systemd-user`.

OpenBSD has its own file: it reads `_PATH_UTMP` with `fread()` in record-sized chunks and skips
records with an empty `ut_name`.

### Windows

`WTSEnumerateSessionsExW(WTS_CURRENT_SERVER_HANDLE, level = 1, …)` returns the session list; only
`WTSActive` entries are kept. Per session, `WTSQuerySessionInformationW(WTSClientAddress)` fills
`clientIp` (`AF_INET` → four bytes formatted as a dotted quad, `AF_INET6` → `RtlIpv6AddressToStringA`)
and `WTSQuerySessionInformationW(WTSSessionInfo)` fills `loginTime` from `LogonTime`. Both are
freed with `WTSFreeMemory()`, and the session array with `WTSFreeMemoryExW()`.
