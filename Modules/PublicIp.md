# PublicIp

> Print your public IP address and related information

| | |
|---|---|
| Module type | `publicip` |
| Default order | 49 (only used by `--gen-config`) |
| Module source | `src/modules/publicip/publicip.c` |
| Detection source | `src/detection/publicip/publicip.c` |

Asks a remote service which address the world sees, and prints it. With the default service the
geolocation that comes back in the same response is appended:

```
Public IP: 203.0.113.42 (Berlin, DE)
```

Without a location the line is just the address. The module performs a real HTTP request, so it needs
network access and it is the slowest module in a default run.

## Platform support

Platform-independent: `src/detection/publicip/publicip.c` is compiled unconditionally and sits on top
of `common/networking`, which has a per-platform backend (`networking_linux.c`,
`networking_windows.c`, …). There is no `nosupport` variant.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `url` | string | `""` | Service to ask. Empty means the built-in default (see below) |
| `timeout` | integer (ms) | `0` | Connection timeout in milliseconds; `0` disables it |
| `ipv6` | boolean | `false` | Resolve and connect over IPv6, and use the IPv6 variant of the default service |
| `key` | string | `Public IP` | Module key. A single space hides the key and the separator |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

With `url` empty the module connects to `ipinfo.io` (or `v6.ipinfo.io` when `ipv6` is `true`) on
**port 80**, requests `/json`, and parses the reply as JSON: the `ip` field becomes the address and
`city` + `country` become the location, joined with `, `.

With a `url` set, the **whole response body** — headers stripped, trailing whitespace trimmed — is the
address, and `location` stays empty. The reply is *not* parsed as JSON in that case, so a service that
returns a JSON object prints the object verbatim; use a service that answers with a bare address
(`http://api.ipify.org`, `http://icanhazip.com`, …).

`url` only accepts `http`. The scheme prefix is optional, but any other scheme aborts the run — see
the pitfalls. An optional `:port` is honoured (default 80), and an IPv6 literal has to be bracketed:
`[2001:db8::1]:8080`.

`timeout` is in milliseconds and only applies when it is greater than `0`; `0` leaves the socket at
the system default, which can make an unreachable service hang for a long time.

## Format string

Run `fastfetch -h publicip-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{ip}` | Public IP address |
| `{location}` | Location |

Neither is marked `*` in the help output, so neither is available in the key format.

`{location}` is empty whenever `url` is set, and also when the default service's `city` / `country`
fields are missing.

## JSON output

```jsonc
[
    {
        "type": "PublicIp",
        "result": {
            "ip": "203.0.113.42",
            "location": "Berlin, DE"
        }
    }
]
```

- On failure the object is `{"type": "PublicIp", "error": "…"}` instead, with no `result` key.
- Both fields are strings; `location` is `""` (never `null`) when unknown.
- The key order inside `result` is `ip`, then `location`.

## Examples

```jsonc
// Address only, no geolocation
{ "type": "publicip", "format": "{ip}" }
```

```jsonc
// A bare-address service, with a 2 second timeout
{ "type": "publicip", "url": "http://api.ipify.org", "timeout": 2000 }
```

```jsonc
// Both families in one run -- this is the only way to have two instances
{ "modules": [ { "type": "publicip" }, { "type": "publicip", "ipv6": true } ] }
```

## Using HTTPS and other unsupported URLs

The module has **no TLS support at all**, and its URL parser is deliberately tiny. Whenever the
endpoint is `https`, or its URL is anything the parser does not accept (a redirect target, a
non-default port on an IPv6 literal, a URL that needs authentication), the answer is to stop using
this module and use [Command](Modules/Command) with `curl` instead. `Modules/Weather` sits on the same
networking layer with the same lack of TLS, but it has no `url` key at all — its host is fixed — so
the workaround below is the only way to reach any other endpoint from it:

```jsonc
{
    "type": "command",
    "key": "Public IP",
    "text": "curl -s --max-time 5 https://api.ipify.org"
}
```

That produces the same line the `publicip` module would:

```
Public IP: 203.0.113.42
```

Points that matter when writing such a module:

- **`shell` and `param` default to the platform shell** — `cmd.exe /c` on Windows and `/bin/sh -c`
  elsewhere — so `text` is written exactly as it would be typed into a shell. `curl` ships with
  Windows 10 and later, so nothing has to be installed there.
- **Always pass an explicit timeout.** The `command` module has no timeout of its own, and an
  unreachable HTTPS endpoint makes curl sit for its own (much longer) default. `--max-time 5` keeps a
  failure from stalling the whole run.
- **A failed request is reported, not hidden.** curl exits non-zero on a connection or TLS error and
  the module then prints `Child process exited with an error` — which, like every module error,
  needs `display.showErrors` to be `true` to be visible.
- **The whole body becomes the value**, so a service that answers with more than the address (a JSON
  object, or `ipip.net`-style text carrying a location) needs either a service that returns a bare
  address or a `format` that trims it. Use `splitLines: true` when the body has several lines.
- **The JSON result is the raw string** under `result`, so a script that consumed
  `PublicIp.result.ip` has to read `Command.result` instead.

## Pitfalls

- **The module can only be used once per address family.** Two `publicip` modules with the same
  `ipv6` value make fastfetch print
  `Error: PublicIp module can only be used once due to internal limitations` to stderr and **exit with
  status 1 before printing anything** — including when the two modules are otherwise identical. One
  `ipv4` plus one `ipv6` instance is fine, because they occupy separate slots. `Modules/Weather` is
  the stricter version of the same restriction: it has a single slot and no address-family split, so
  its second instance always aborts the run.
- **A `url` with an unsupported scheme kills the whole run.** `https://…` prints
  `Error: only http: protocol is supported. Use \`Command\` module with \`curl\` if needed` and exits
  with status 1, before anything else is printed. There is no TLS support here and no way to relax the
  check — see [Using HTTPS and other unsupported URLs](#using-https-and-other-unsupported-urls) for
  the recommended replacement.
- **A malformed port or an unbalanced `[` does the same.** Invalid values in the URL are reported with
  `invalid port`, `unmatched '['` or `unexpected characters after the IPv6 literal` and exit with
  status 1. Bare IPv6 literals without brackets are treated as having no port, so
  `http://2001:db8::1` is *not* parsed as host + port and will not resolve as intended.
- **The request is sent while the configuration is parsed, not when the module runs.**
  `ffPreparePublicIp()` issues the HTTP request as soon as the module is seen in the config (or in
  `--gen-config`-style preparsing), and the response is only read later, when the module is actually
  printed. A run that aborts before reaching the module still performed the request. `Modules/Weather`
  is built the same way, so the two modules share this timing.
- **The default service only speaks plain HTTP on port 80.** There is no fallback and no retry; if the
  network blocks it, the module reports the connection error. A custom `url` also has to be plain
  HTTP for the same reason.
- **A service that answers with an empty body yields `Empty server response received`**, which is an
  error, not an empty line. This is a common outcome for a URL that redirects or that requires HTTPS.
- **`location` is a free-form `"city, country"` string.** It is built from whatever the default
  service's JSON contains; entries that are missing become the empty string, and the join still emits
  the `, `. Nothing validates or normalises the country code.
- **The `url` default documented in `doc/json_schema.json` does not match the runtime.** The schema
  advertises `http://ipinfo.io/ip`, but the actual default is an *empty* string, which selects
  `ipinfo.io/json` and therefore also produces `location`. Setting the URL to
  `http://ipinfo.io/ip` explicitly gives a different result: no location at all.
- **Errors are invisible by default.** Connection and parse failures go through `ffPrintError()`, so
  with `display.showErrors` at its default `false` the module simply prints nothing.

## Implementation

`FFPublicIpResult` holds two strings, `ip` and `location`. The module keeps two static
`FFNetworkingState`s — one per address family — and two static status pointers, which is exactly why
the module cannot be instantiated twice for the same family.

`ffPreparePublicIp()` fills in `state->timeout` and `state->ipv6` and calls
`ffNetworkingSendHttpRequest()`. With no `url` it enables HTTP compression and TCP Fast Open and asks
`ipinfo.io:80` for `/json`. With a `url` it strips an optional `http://` prefix (any other scheme
aborts the process), splits the path off at the first `/`, splits an optional port off the host —
from inside `[...]` for a bracketed literal, or after the single colon of an unbracketed host — and
then issues the request with path `/` when none was given.

`ffDetectPublicIp()` reads the response, resets the state and the status so the slot becomes reusable
in a later round, skips everything up to the first `\r\n\r\n` to drop the headers, and then either
parses the JSON (default service only) or moves the trimmed body into `ip`. When the default service's
reply is not valid JSON the raw body is used as the address, so a plain-text answer from a custom
endpoint also works.
