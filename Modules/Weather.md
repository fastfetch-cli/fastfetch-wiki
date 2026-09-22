# Weather

> Print weather information

| | |
|---|---|
| Module type | `weather` |
| Default order | 66 (only used by `--gen-config`) |
| Module source | `src/modules/weather/weather.c` |
| Detection source | `src/detection/weather/` |

Prints the current weather for a location, as one line of text produced by the
[wttr.in](https://wttr.in) service. The key is `Weather`.

```
Weather: +21°C - Partly cloudy  (Berlin, DE)
```

That line is the server's answer to the default request format `%t+-+%C+(%l)`, decoded and printed
verbatim — including the server's own spacing, which is why the location appears after two spaces.

This module is **not** a local detector: it needs a working network connection, it talks to one
hard-coded host, and the text it prints is whatever that host returns.

## Platform support

`src/detection/weather/weather.c` is platform-independent and listed in the shared part of
`CMakeLists.txt`, so the module exists on every platform the same way. There is no per-platform
backend and no `weather_nosupport.c`.

| Platform | Implementation | Notes |
|---|---|---|
| All | `weather.c` | HTTP over the shared networking layer (`ffNetworkingSendHttpRequest`) |

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `location` | string | empty | Location to report on. Must be URI-encoded; empty lets the server guess from the client address. |
| `outputFormat` | string | `%t+-+%C+(%l)` | The service's own format string. Must be URI-encoded. |
| `timeout` | integer | `0` | Milliseconds to wait for the server. `0` disables the timeout. |
| `key` | string | `Weather` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

`location` is a *path segment*, not a query parameter: it is placed directly after the `/` of the
request path, so it has to be URI-encoded (`Berlin` works as-is, `New York` has to be `New+York` or
`New%20York`).

`outputFormat` is a wttr.in format string, not a fastfetch one. The default `%t+-+%C+(%l)` asks for
the temperature, a literal ` - `, the condition and the location in parentheses. The value is
appended to `?format=` in the request path, so it has to be URI-encoded as well — which is why the
default spells a space as `+`.

`timeout` is handed to the networking layer, which applies it to the connection attempt and to the
wait for the answer. `0`, the default, means "wait forever", so a network that silently drops packets
makes fastfetch hang rather than fail.

The unit is not a key of this module: it comes from `display.temp.unit`, whose CLI spelling is
`--temp-unit`. `C`/`CELSIUS` appends `&m` to the request and `F`/`FAHRENHEIT` appends `&u`; the
default (`D`) and `K`/`KELVIN` append nothing and let the server pick, which means **Kelvin does not
work** — the answer comes back in the server's own default unit.

## Format string

Run `fastfetch -h weather-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{result}` | The complete weather line |

`{result}` is the only variable and it carries no `*` marker, so nothing is available in the key
format. Because the whole line is one string, a custom `format` can decorate it but cannot
rearrange it; use `outputFormat` for that.

## JSON output

```jsonc
{
    "type": "Weather",
    "result": "+21°C - Partly cloudy  (Berlin, DE)"
}
```

`result` is a **string**, not an object: the module has no structured weather data to offer. On
failure the object is `{ "type": "Weather", "error": "…" }`, with errors such as
`Empty server response received` or the networking layer's own message.

## Examples

```jsonc
// A fixed location
{ "type": "weather", "location": "Berlin" }
```

```jsonc
// A compact line, with the location resolved by the server
{ "type": "weather", "outputFormat": "%l:+%c+%t" }
```

```jsonc
// Give up after two seconds instead of waiting forever
{ "type": "weather", "timeout": 2000 }
```

```jsonc
// Decorate the server's answer
{ "type": "weather", "location": "Berlin", "format": "WX[{result}]" }
```

## Compared with PublicIp

`Modules/PublicIp` is the other module that asks a remote service during the prepare step and prints
the answer later. The two share the shape but not the flexibility:

| | Weather | PublicIp |
|---|---|---|
| Service | fixed: `wttr.in:80` | `ipinfo.io:80/json`, or any `http://` URL from `url` |
| Single use | once per process, always | once per address family (`ipv4` + `ipv6` both work) |
| Response handling | opaque text, printed verbatim | JSON parsed for the default service; the whole body is the address for a custom `url` |
| HTTP compression, TCP Fast Open | off | on for the default service |
| TLS | none | none |
| Replacement when the endpoint is unsuitable | `Modules/Command` with `curl` | same |

Because there is no `url` key here, a different weather service or an HTTPS endpoint means leaving
this module behind. The recipe — and the points that matter when writing it, such as always passing
`--max-time` — is written up in
[Using HTTPS and other unsupported URLs](Modules/PublicIp#using-https-and-other-unsupported-urls).

## Pitfalls

- **The module can only be used once per run.** A second `weather` module aborts the whole process
  with `Error: Weather module can only be used once due to internal limitations` and exit status 1,
  before anything is printed. The check lives in the prepare step, so it fires even when the module
  would not have been reached. Two different locations therefore cannot be shown in one run — unlike
  `Modules/PublicIp`, which has one slot per address family and so tolerates an `ipv4` plus an `ipv6`
  instance. Use a `command` module for anything more involved.
- **The request is sent while the configuration is parsed, not while printing.** The prepare step
  opens the connection and writes the request; the print step reads the answer. Two consequences: the
  round trip overlaps with the rest of the run, so the module is often free by the time it prints;
  and the module's position in the configuration does not determine when the network is used.
- **The host is hard-coded.** `wttr.in`, port 80, plain HTTP. There is no way to point the module at
  another service, no HTTPS and no proxy support — the request is a bare socket connection from the
  shared networking layer. `User-Agent: curl/0.0` is sent because wttr.in answers differently (or
  with a bot notice) for unrecognised clients.
- **`timeout: 0` means "no timeout".** The default disables it entirely, so an unreachable server
  hangs fastfetch instead of producing an error. Every other value is a hard limit in milliseconds.
- **Nothing in the answer is validated.** The response body is taken as everything after the first
  blank line and is only right-trimmed; there is no parsing, no status-code check beyond what the
  networking layer does, and no shape check. An HTML error page from the service is printed as if it
  were the weather.
- **An empty body is the only content-level error.** `Empty server response received` is raised when
  the body is empty after trimming; a body that is wrong but non-empty is printed.
- **The location can be resolved by the server rather than by the client.** With an empty `location`
  the path starts with `/`, and wttr.in geolocates the caller's address. That address is the public
  one, so the reported location is the network's, not necessarily the machine's.
- **`display.temp.unit` is the only way to ask for Fahrenheit**, and it is a display-wide option:
  every module that reports a temperature follows it. There is no per-module override.
- **The value is a string in the JSON result.** Consumers that want a number have to parse the line
  themselves; the module deliberately does not re-parse what the service formatted.

## Implementation

`ffPrepareWeather()` builds the request path — `/`, the optional `location`, `?format=`, the
`outputFormat`, and `&m` or `&u` for the temperature unit — and hands it to
`ffNetworkingSendHttpRequest()` against `wttr.in:80`. The request state and the send result live in
file-scope statics, and the "already used" test is a sentinel value in those statics rather than a
flag in the options, which is why the restriction is per process and not per module instance.

`ffDetectWeather()` calls `ffPrepareWeather()` if the prepare step was skipped, returns the stored
send error if there was one, and otherwise reads the response with
`ffNetworkingRecvHttpResponse()`, resets the state so the next run starts clean, cuts the string
after the first `\r\n\r\n` and trims trailing whitespace. A body that is empty after that is
reported as an error; anything else is the result.
