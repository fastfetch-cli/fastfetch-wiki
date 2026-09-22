# Wifi

> Print connected Wi-Fi info (SSID, connection and security protocol)

| | |
|---|---|
| Module type | `wifi` |
| Default order | 52 (only used by `--gen-config`) |
| Module source | `src/modules/wifi/wifi.c` |
| Detection source | `src/detection/wifi/` |

Prints one line per Wi-Fi interface. The printed key is `Wi-Fi`, and it is numbered when more than
one interface is present.

```
Wi-Fi: MyNetwork - 802.11ax (Wi-Fi 6) - 5 GHz - WPA2-PSK (80%)
```

The line is `<ssid> - <protocol> - <band> - <security>` followed by the signal quality in
parentheses. Fields that the platform did not report are dropped along with their separator, so a
2.4 GHz connection simply has no `2.4 GHz` part. When there is no SSID to print — the interface is
down, or nothing is associated — the interface status takes the SSID's place instead:

```
Wi-Fi: Disconnected
```

The signal quality is the only part that follows `display.percent.type`; with the `bar` bit enabled
its bar is prepended to the whole line, and with `hide-others` everything except the bar and the
number is suppressed.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `wifi_linux.c` | nl80211 over generic netlink, falling back to the wireless-extension ioctls |
| Android | `wifi_android.c` | Talks to the `wifi` system service over raw binder and parses the returned parcel |
| FreeBSD | `wifi_bsd.c` | `SIOCG80211` ioctls on interfaces named `wlan*` |
| NetBSD | `wifi_nbsd.c` | Same ioctls, on interfaces named `iwm*` |
| OpenBSD | `wifi_obsd.c` | `SIOCG80211NODE` on every interface, filtering out the ones that reject it |
| Solaris / illumos | `wifi_sunos.c` | `/dev/wifi/*` character devices, `WLAN_GET_PARAM` streams ioctls |
| Haiku | `wifi_haiku.cpp` | `BNetworkRoster` plus `BNetworkDevice` |
| macOS | `wifi_apple.m` | `CoreWLAN`, with the frequency read from the IORegistry |
| Windows | `wifi_windows.c` | `WlanOpenHandle` / `WlanEnumInterfaces` / `WlanQueryInterface` |
| DragonFly | `wifi_nosupport.c` | Reports `Not supported on this platform` |
| GNU/Hurd | `wifi_nosupport.c` | Same file |

DragonFly and GNU/Hurd share `src/detection/wifi/wifi_nosupport.c`, which returns a fixed error
message; every other platform has a real backend.

## Configuration

The module has no keys of its own.

| Key | Type | Default | Description |
|---|---|---|---|
| `percent` | object | `green: 75`, `yellow: 50` | Colour thresholds for the signal quality |
| `key` | string | `Wi-Fi` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

The thresholds are **inverted** relative to most percentage modules: `green: 75` is higher than
`yellow: 50`, which makes a strong signal green. With `green > yellow` the meaning is `[green, 100]`
green, `[yellow, green)` yellow and `[0, yellow)` red.

`percent.type` defaults to `0`, which means "use `display.percent.type`" — by default the number and
its colour, no bar. The bits behave as they do everywhere else: `num` alone prints the number without
colour, `num-color` adds the colour, `bar` replaces the trailing `(n%)` with a bar in front of the
line, `hide-others` drops the SSID, protocol, band and security, and an empty list hides both the
number and the bar.

The bar is coloured from `display.bar.color.elapsed` / `.total` / `.border`, **not** from
`display.percent.color`. The elapsed section is split at the two thresholds, so with the default
`green: 75` / `yellow: 50` on a ten-cell bar the first five cells are red, the next three yellow and
the green section starts at cell eight. A bar whose width and thresholds make the green section fall
past the last filled cell shows no green at all, even though `{signal-quality}` is coloured green for
the same value — which is exactly what a 83% signal does with the defaults.

## Format string

Run `fastfetch -h wifi-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{inf-desc}` | Interface description |
| `{inf-status}` | Interface status |
| `{status}` | Connection status |
| `{ssid}` | Connection SSID |
| `{bssid}` | Connection BSSID |
| `{protocol}` | Connection protocol |
| `{signal-quality}` | Signal quality, formatted as a percentage |
| `{rx-rate}` | Receive rate in Mbit/s |
| `{tx-rate}` | Transmit rate in Mbit/s |
| `{security}` | Security algorithm |
| `{signal-quality-bar}` | Signal quality as a percentage bar |
| `{channel}` | Channel number |
| `{channel-width}` | Channel width in MHz |
| `{band}` | Channel band, as a bare number |

No variable carries the `*` marker, so **nothing is available in the key format**: `key: "W[{ssid}]"`
prints the literal text `W[{ssid}]` on every line.

`{signal-quality}` and `{signal-quality-bar}` are the only variables that are filled conditionally by
`display.percent.type`: the number is empty when the `num` bit is off and the bar is empty when the
`bar` bit is off. `{band}` holds the number only — `5`, `2.4`, `4.9`, `3.65`, `6`, `45`, `60`, `0.9`
or an empty string — while the default output appends `GHz` itself.

## JSON output

```jsonc
{
    "type": "Wifi",
    "result": [
        {
            "inf": {
                "description": "wlan0",
                "status": "up"
            },
            "conn": {
                "status": "connected",
                "ssid": "MyNetwork",
                "bssid": "AA:BB:CC:DD:EE:FF",
                "protocol": "802.11ax (Wi-Fi 6)",
                "security": "WPA2/WPA3",
                "signalQuality": 78.0,
                "rxRate": 1200.0,
                "txRate": 866.7,
                "channel": 36,
                "channelWidth": 80,
                "frequency": 5180
            }
        }
    ]
}
```

`signalQuality`, `rxRate` and `txRate` are `null` when the platform could not report them. All three
are stored as `-DBL_MAX`, but the two text paths render that sentinel differently:
`{signal-quality}` goes through the percentage formatter and becomes a single `-`, while `{rx-rate}`
and `{tx-rate}` are passed as raw doubles and become the literal `-1.7976931348623157e308` — the
`display.fraction` settings do not apply, because the value is out of fixed-point range. The other
three connection numbers are `0` when unknown, because zero is a valid channel, width and frequency
for "nothing to report".

An interface that exists but is not connected still appears in the array, with an empty `conn` — the
text output prints `inf.status` in that case. The JSON writer does not check for an empty list, so a
machine without a Wi-Fi interface answers with `"result": []` while the text path reports
`No Wifi interfaces found`.

## Examples

```jsonc
// A bar before the SSID, plus the percentage
{ "type": "wifi", "percent": { "type": ["num", "bar", "num-color"] } }
```

```jsonc
// Only the numbers, without the SSID and the protocol
{ "type": "wifi", "percent": { "type": ["num", "hide-others"] } }
```

```jsonc
// One line per interface, in a fixed shape
{ "type": "wifi", "format": "{inf-desc} {ssid} {band}GHz ch{channel} {signal-quality}" }
```

```jsonc
// Guard the fields that can be unknown
{ "type": "wifi", "format": "{ssid}{?tx-rate} @ {tx-rate} Mbit/s{?}" }
```

## Pitfalls

- **`{inf-status}` and `{status}` are spelled differently on every platform.** Linux writes `up`,
  `down`, `unknown`, `connected` and `disconnected` in lower case; Android capitalises the interface
  state but not the connection state; Windows uses the `WLAN_INTERFACE_STATE` names (`Not ready`,
  `Connected`, `Disconnected`, `Associating`, `Authenticating`, `Discovering`, `Disconnecting`,
  `Ad hoc network formed`); macOS uses `Power On`/`Power Off` and `Active`/`Inactive`; the BSDs use
  `Up`/`Down`/`Unknown` and `Associated`/`Not associated`; Solaris and Haiku use `Up`/`Down` with
  `Connected`/`Not connected`. A format that tests these strings only works on the platform it was
  written for.
- **`{status}` is empty on Windows whenever the interface is not connected.** The connection
  attributes are only queried after the interface state has been read as `connected`, so a
  disconnected adapter has a filled `inf.status` and an empty `conn.status`.
- **An unknown rate leaks a sentinel into a custom format.** `{rx-rate}` and `{tx-rate}` print
  `-1.7976931348623157e308` when the platform did not report them, because the variable carries the
  raw double. The JSON result writes `null` for the same state, and the percentage formatter renders
  its own sentinel as a plain `-` with a bar made of the "total" character. Guard the two rates with
  a conditional block, as in the example above.
- **A conditional block does not guard `{signal-quality}`, and does guard `{channel-width}`.** The
  three sentinels are tested differently, so `{?...}` does not answer the same question for each:
  `{?rx-rate}` is false when the rate is unknown *and* when it is a real `0`, because a double counts
  as set only when it is `> 0`; `{?channel-width}` is false whenever the width is `0`, which on
  Windows is the ordinary case, so a guarded block around it silently disappears; and
  `{?signal-quality}` is **true** for an unknown signal, because the percentage formatter has already
  turned the sentinel into a non-empty `-`. An interface that is not connected therefore renders
  `sq=-` rather than dropping the block.
- **`{channel-width}` can be `0` even when the platform answers.** On Windows the value comes from
  `ulBandwidth` of the realtime-connection-quality query, and a driver that does not fill that field
  leaves it at `0` — a 160 MHz-capable adapter associated on a 160 MHz channel still reports `0`. The
  query succeeding and the width being present are two different things, so `0` must be read as
  "not reported" rather than as a measurement.
- **`{band}` is a bare number.** The default output adds `GHz`; a custom format that writes
  `{band}GHz` reproduces it, but one that writes `{band}` alone prints `5`. The value is derived from
  the frequency, not from the channel, and is empty when the frequency is unknown.
- **The default line's shape cannot be reproduced with `{format}` alone.** The SSID, protocol, band
  and security parts are conditional and the percentage is parenthesised, and the parentheses are
  omitted when everything before it was suppressed — for example with `hide-others`. A custom format
  has to reimplement that logic.
- **The line falls back to the interface status when the SSID is empty.** This is why a disconnected
  machine prints `Wi-Fi: Disconnected` and why a custom `format` that only prints `{ssid}` renders an
  empty line there.
- **The security strings are not portable.** Windows reports `WPA2-PSK`, `WPA3-SAE`, `OWE`,
  `802.11X`; macOS reports `WPA2 Personal`, `WPA3 Enterprise`; Android reports `WPA2-PSK`,
  `WPA3-ENT-192`, `WAPI-PSK`; Haiku reports `Encrypted` for an encrypted network with no
  authentication mode; the BSDs report the ioctl's own vocabulary (`WPA2`, `WPA1+2`, `802.1x`);
  Linux joins everything the beacon advertises into a `/`-separated list such as `WPA2/WPA3`.
  `Insecure` is the one spelling every backend agrees on.
- **Linux changes its own answer depending on which transport worked.** nl80211 is tried first and
  the wireless-extension ioctls then fill only the fields that are still empty. When netlink is
  unavailable — a kernel without `nl80211`, a sandbox that blocks generic netlink — the security
  string degrades from the beacon-derived list to the negotiated cipher (`WEP`, `TKIP`, `CCMP`,
  `PMK`, `CMAC`), and `{protocol}` falls back to the `SIOCGIWNAME` string.
- **The two rate variables are not both available on the BSDs.** FreeBSD fills `{rx-rate}` from the
  station's `isi_txmbps` and never sets `{tx-rate}`; NetBSD does the opposite, setting `{tx-rate}`
  from `isi_txrate`. OpenBSD sets neither.
- **`{channel-width}` is always `0` on OpenBSD and Solaris**, and on Windows it is `0` whenever the
  driver leaves `ulBandwidth` empty — see the dedicated bullet above. `{channel}` and `{frequency}`
  are filled from separate queries on Windows, so one can be present while the other is `0`.
- **Linux only sees interfaces that register a `phy80211` directory.** The gate is
  `/sys/class/net/<ifname>/phy80211/`, so a driver that does not use mac80211 is invisible even
  though it is a Wi-Fi interface. If `operstate` cannot be read at all, the interface is reported as
  `unknown`/`disconnected` and neither transport is consulted.
- **The signal quality is a percentage of different things.** Most backends map RSSI with the same
  piecewise rule (`-50 dBm` or better is 100, `-100 dBm` or worse is 0, linear in between); OpenBSD
  divides the reported RSSI by the reported maximum, Solaris divides by `MAX_RSSI`, Haiku takes the
  driver's own `signal_strength` clamped to 100, and Windows uses `wlanSignalQuality` as-is. The
  numbers are comparable in range but not in meaning.
- **Android reports at most one interface**, and replaces an SSID or BSSID that the system service
  withheld with `<redacted>` rather than leaving it empty — an empty SSID is what the module prints
  the interface state in place of, and the reply still carries a signal, a channel and a rate that
  are worth showing. The same placeholder is used for a connection that genuinely has no address.
- **The text path fails on an empty interface list but the JSON path does not.** With no Wi-Fi
  interface at all the text output is an error (`No Wifi interfaces found`) and the JSON result is an
  empty array. This is the same split described in `Modules/Keyboard`.

## Implementation

Every backend fills a list of `FFWifiResult`, each with an interface half (`description`, `status`)
and a connection half (`status`, `ssid`, `bssid`, `protocol`, `security`, `signalQuality`, `rxRate`,
`txRate`, `channel`, `channelWidth`, `frequency`). An interface that is present but not associated is
still added, with the connection fields left empty and the three numeric quality fields set to
`-DBL_MAX`. `ffPrintWifi()` then derives the band string from the frequency with fixed thresholds
(`>58000` is `60`, `>40000` is `45`, `>5900` is `6`, `>5100` is `5`, `>4900` is `4.9`, `>3600` is
`3.65`, `>2000` is `2.4`, `>800` is `0.9`) and renders either the default line or the format string.

### Linux

Interfaces are enumerated with `if_nameindex()` and filtered on the `phy80211` directory. For an
interface whose `operstate` is `up`, `detectWithNetlink()` runs first: a generic netlink socket is
opened, the `nl80211` family id is resolved through `CTRL_CMD_GETFAMILY`, the associated BSS is found
with a scan dump, and the interface and station dumps supply the protocol, the channel width, the
rates and the security from the RSN and WPA information elements. `detectWithIoctl()` then fills in
whatever is still unknown from `SIOCGIWESSID`, `SIOCGIWNAME`, `SIOCGIWAP`, `SIOCGIWRATE`,
`SIOCGIWFREQ`, `SIOCGIWSTATS` and `SIOCGIWENCODEEXT`. A failed netlink initialisation is latched in
a sentinel value so it is not retried per interface. The interface state for a non-`up` interface
comes from the `IFF_UP` bit of `/sys/class/net/<ifname>/flags`.

### Android

`detectWithBinder()` resolves the `wifi` service through the service manager, looks the transaction
code for `getConnectionInfo` up in the framework jar with the DEX parser, and calls it with the
process's own package name. The reply is a Java parcel, which is not self-describing, so the fields
are recognised by shape: a length-prefixed UTF-16 MAC address for the BSSID, one of the supplicant
state names for the state, an integer naming a Wi-Fi standard followed by two plausible link speeds
for the protocol, and an enum for the security type. The interface flags come from `getifaddrs()`,
falling back to `getWifiEnabledState` when the interface has no address — which is exactly the state
in which `getifaddrs()` omits it.

### FreeBSD, NetBSD and OpenBSD

FreeBSD and NetBSD iterate `if_nameindex()` and filter on a name prefix (`wlan`, `iwm`), then use
`IEEE80211_IOC_SSID`, `IEEE80211_IOC_BSSID`, the current-channel request, `IEEE80211_IOC_STA_INFO`
and `IEEE80211_IOC_AUTHMODE` — NetBSD redefines a few `IEEE80211_IS_CHAN_*` macros because its
`net80211` headers differ. OpenBSD has no name filter: it tries `SIOCG80211NODE` on every interface
and treats `ENXIO`, `ENODEV`, `EINVAL` and `ENOTTY` as "not a Wi-Fi interface", then reads
`SIOCG80211CHANNEL`, `SIOCG80211WPAPARMS` and `SIOCG80211NWKEY`.

### Solaris and illumos

`/dev/wifi` is walked for entries whose name ends in a digit, each device is opened and checked with
`isastream()`, and every value is fetched with a `WLAN_GET_PARAM` streams ioctl into a shared
`wldp_t` buffer: the radio state, the link status, the ESSID, the BSSID, the RSSI, the PHY
configuration and the encryption mode. A missing `/dev/wifi` is not an error — the function returns
success with an empty list.

### Haiku

`BNetworkRoster::Default()` enumerates the interfaces, `BNetworkDevice::IsWireless()` filters them,
and the associated network is read with `GetNextAssociatedNetwork()`. The protocol is never filled,
the channel and frequency are left at zero, and the BSSID comes from the network's link-level
address.

### macOS

`CWWiFiClient.sharedWiFiClient.interfaces` provides the interface list. The SSID, BSSID and protocol
come from CoreWLAN, with the security enum mapped to names; since macOS redacts the SSID and BSSID
for an unprivileged caller, the module falls back to the saved network profile's SSID and BSSID list
and finally to the literal `<redacted>`. The signal quality is derived from the RSSI with the same
piecewise rule as Linux, and the frequency is read from the `IO80211ChannelFrequency` IORegistry
property, walking up the service plane until it is found.

### Windows

`WlanOpenHandle` and `WlanEnumInterfaces` produce the interface list, and for each interface that is
`connected` the module queries the current connection for the SSID, BSSID, PHY type, signal quality,
rates and authentication algorithm. The channel width and centre frequency come from the
realtime-connection-quality query, which is not documented in the public SDK and is therefore
declared locally; the best link of an MLO connection is chosen by RSSI. If that query does not
produce a frequency, the BSS list is consulted instead — and when the list comes back empty the
frequency stays `0`, which is why a custom format prints no band in that case. The channel number
comes from a separate `wlan_intf_opcode_channel_number` query. Every buffer the API hands out is
released with `WlanFreeMemory`, including a BSS list that succeeded with zero entries.
