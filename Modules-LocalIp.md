# LocalIp

> List local IP addresses (IPv4 or IPv6), MAC addresses, etc

| | |
|---|---|
| Module type | `localip` |
| Default order | 50 (only used by `--gen-config`) |
| Module source | `src/modules/localip/localip.c` |
| Detection source | `src/detection/localip/` |

The key is `Local IP` followed by the interface name in parentheses, and the value is the address
line of that interface.

```
Local IP (Ethernet): 192.168.1.13/24
```

By default only the interface carrying the default route is listed, only its IPv4 address, and only
the first address of that family. Every other family and interface needs an option to be turned on.

```
Local IP (VMware Network Adapter VMnet1): 192.168.254.1/24
Local IP (VMware Network Adapter VMnet8): 192.168.88.1/24
```

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `localip_linux.c` | `getifaddrs()`; speed via `SIOCETHTOOL`, prefix from the netmask |
| Windows | `localip_windows.c` | `GetUnicastIpAddressTable()` + `GetIfEntry2()` |
| macOS | `localip_linux.c` + `localip_apple.c` | `getifaddrs()`; speed via `SIOCGIFMEDIA`; the MAC address falls back to the IORegistry |
| FreeBSD / OpenBSD / NetBSD / DragonFly | `localip_linux.c` | `getifaddrs()`; speed via `SIOCGIFMEDIA` |
| Solaris / illumos | `localip_linux.c` | `getifaddrs()`; speed from the `link` kstat |
| Haiku | `localip_linux.c` | `getifaddrs()` |
| GNU/Hurd | `localip_linux.c` | `getifaddrs()`; MAC via `SIOCGIFHWADDR` |
| Android | `localip_linux.c` | Same code; `defaultRouteOnly` is off by default (permission denied) |

Only Windows has a backend of its own; macOS adds `localip_apple.c` on top of the shared file, which
every other platform uses as it is.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `showIpv4` | boolean | `true` | Show IPv4 addresses |
| `showIpv6` | boolean or string | `false` | Show IPv6 addresses. `true` shows the most useful one, `false` none, or one of `"gua"`, `"ula"`, `"lla"`, `"unknown"` to restrict the address kind |
| `showMac` | boolean | `false` | Show MAC addresses |
| `showLoop` | boolean | `false` | Include loopback interfaces (`127.0.0.1`, `::1`) |
| `showPrefixLen` | boolean | `true` | Append the network prefix length (`/24`) |
| `showMtu` | boolean | `false` | Show the MTU |
| `showSpeed` | boolean | `false` | Show the link speed |
| `showFlags` | boolean | `false` | Show the interface flags |
| `showAllIps` | boolean | `false` | List every address bound to an interface instead of the first one |
| `compact` | boolean | `false` | Print every interface on a single line |
| `defaultRouteOnly` | boolean | `true` | Only list interfaces used by the default route |
| `namePrefix` | string | – | Only list interfaces whose name starts with this prefix |
| `key` | string | `Local IP` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

`showIpv6` is the only option that is not a plain boolean: a string both enables IPv6 and filters the
address kind. `"gua"` keeps global unicast addresses (`2000::/3`), `"ula"` unique local
(`fc00::/7`), `"lla"` link-local (`fe80::/10`) and `"unknown"` anything else — IPv4-mapped addresses
and the like.

`namePrefix` is a plain byte-wise prefix, not a glob and not a substring, and it is applied to the
interface name as the platform reports it (on Windows that is the localized adapter name, so
`"eth"` never matches `以太网`).

## Format string

Run `fastfetch -h localip-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{ipv4}` | IPv4 address (or the comma-joined list of them) |
| `{ipv6}` | IPv6 address (or the comma-joined list of them) |
| `{mac}` | MAC address * |
| `{ifname}` | Interface name * |
| `{is-default-route}` | The address families the interface carries the default route for: `ipv4`, `ipv6` or `ipv4,ipv6` |
| `{mtu}` | MTU in bytes |
| `{speed}` | Link speed, pre-formatted (`1 Gbps`, `100 Mbps`) |
| `{flags}` | Interface flags |

`{mac}` and `{ifname}` are the two variables marked with a `*`, i.e. also available in the key format.
A `key` such as `IP [{ifname}]` therefore works, and `{index}` / `{icon}` / `{module-name}` are
available there too.

`{speed}` is not the raw number: it is pre-formatted by the module, with `Tbps` at 1000000 Mbps and
above, `Gbps` at 1000 Mbps and above, and `Mbps` below that — the fractional digits follow
`display.fraction`. It is empty when the speed could not be read.

`{is-default-route}` is a plain string listing the families the interface carries the default route
for, lower-cased and comma-separated without a space: `ipv4`, `ipv6` or `ipv4,ipv6`. An interface
that is the default route for neither family gets an empty string, which makes
`{?is-default-route}…{/is-default-route}` behave as expected.

## JSON output

```jsonc
[
    {
        "type": "LocalIp",
        "result": [
            {
                "name": "Ethernet",
                "defaultRoute": { "ipv4": true, "ipv6": false },
                "ipv4": "192.168.1.100/24",
                "ipv6": "2001:db8::1/64",
                "mac": "aa:bb:cc:dd:ee:ff",
                "mtu": 1500,
                "speed": 1000,
                "flags": "UP,BROADCAST,RUNNING,MULTICAST"
            }
        ]
    }
]
```

- `result` is always an array, one object per interface.
- Only the keys whose option is enabled are emitted: `ipv4` needs `showIpv4`, `ipv6` needs
  `showIpv6`, and so on. `name` is always present.
- The `defaultRoute` object is only emitted when `showIpv4` or `showIpv6` is on, and it carries only
  the enabled families as booleans.
- `ipv4` and `ipv6` are comma-joined **strings**, not arrays — one entry per address found by
  `showAllIps`. The `/N` suffix is part of the string.
- `mtu` and `speed` are numbers in bytes and Mbps. An interface the platform cannot describe keeps
  the sentinel `-1` for both of them — `speed` is `-1`, not `0`, when the link speed could not be
  read.

## Examples

```jsonc
// Every IPv6 address of every interface, with the MAC address, in one line
{ "type": "localip", "showIpv6": true, "showAllIps": true, "showMac": true, "compact": true }
```

```jsonc
// A compact one-liner limited to the wired interface
{ "type": "localip", "namePrefix": "en", "showIpv6": "gua", "showMtu": true, "showSpeed": true }
```

```jsonc
// A custom line: interface name, address and directory of runtime flags
{ "type": "localip", "format": "{ifname}: {ipv4} ({flags})", "showFlags": true }
```

```jsonc
// Hide the loopback address unless it is the only one
{ "type": "localip", "showLoop": false, "defaultRouteOnly": false }
```

## Pitfalls

- **The JSON result is not sorted, but the text output is.** `ffPrintLocalIp()` sorts the list by
  interface name before printing; `ffGenerateLocalIpJsonResult()` does not, so the array keeps the
  order the platform reported its interfaces in. The two outputs can list the same interfaces in
  different orders.
- **`{is-default-route}` is a family list, not a boolean.** It prints `ipv4`, `ipv6` or `ipv4,ipv6`,
  and an **empty string** when the interface is the default route for neither family — it never prints
  `true` / `false`. A format that wants a boolean has to test for an empty value instead. Note that
  the variable is only rendered in the custom-`format` path; the default line marks the default route
  with a trailing `*` instead.
- **A run that matches no interface prints nothing by default.** When `namePrefix` filters every
  interface out — or no interface qualifies as the default route — the module reports
  `Failed to detect any IPs`, but `display.showErrors` defaults to `false`, so the text run is
  silently empty (and the JSON is `"result": []` with no `error` field). Add
  `"display": { "showErrors": true }` to see the message.
- **The interface name is whatever the platform calls it.** On Linux and the BSDs it is the kernel
  name (`eth0`, `enp3s0`, `wlan0`); on Windows it is the adapter's friendly name, which is localized
  (`Ethernet`, `以太网`, `Wi-Fi`). `namePrefix` matches the same string, so a prefix that works on
  one machine may match nothing on another.
- **`defaultRouteOnly` is ignored on Android.** The option is only enabled by default on non-Android
  builds (the default-route lookup needs a permission the app usually does not have), and enabling it
  by hand has no effect there. Every interface is listed.
- **The `[Speed … / MTU …]` and `<flags>` brackets are dropped when empty.** The address line only
  gains the brackets when at least one value inside them was read, so an interface with an unknown
  speed and an unread MTU silently loses that part instead of printing `Speed 0`.
- **Multiple addresses of one family are comma-joined without a space.** With `showAllIps`, an
  interface carrying two IPv6 addresses prints `2001:db8::1/64,fe80::1/64` in both the text line and
  the JSON string.
- **`compact` only changes the text path.** With `compact: true` the JSON is still the usual array;
  only the text collapses to one line, with the interfaces joined by ` - ` and the per-interface key
  dropped in favour of a single `Local IP` key.
- **On macOS the MAC address comes from the IORegistry, and not every interface has one.** macOS 27
  hands `02:00:00:00:00:00` to every process that is not signed as an Apple platform binary — which
  includes ad-hoc and linker-signed builds, so your own `ifconfig` shows the placeholder for every
  interface while fastfetch shows the real address. fastfetch replaces the placeholder through the
  IORegistry, but only where an `IOMACAddress` exists: on a typical Mac that is `en*`, `anpi*`,
  `ap1` and `vmenet*`, while `awdl0`, `bridge0`, `bridge100` and `llw0` keep
  `02:00:00:00:00:00`. The value recovered this way is the **hardware** address, so it differs from
  what a platform binary reports while Private Wi-Fi Address is turned on.
- **Windows no longer reports an interface that has no unicast address.** The interfaces are
  enumerated from the unicast address table, so an adapter that carries only a MAC is invisible —
  not even `showMac` brings it back.

## Implementation

`ffDetectLocalIps()` fills a list of `FFLocalIpResult` (five `FFstrbuf`s — `name`, `ipv4`, `ipv6`,
`mac`, `flags` — plus `mtu`, `speed` and a `defaultRoute` bit field) and returns an error string.
`ffPrintLocalIp()` and `ffGenerateLocalIpJsonResult()` each call it themselves, so a
`--dynamic-interval` run re-probes the interfaces every round; nothing is cached.

The default `showType` is IPv4 | prefix length | default-route-only. The last bit is left out on
Android, where the default-route lookup needs a permission the app usually does not have.

### Windows

The interfaces are enumerated from the **unicast address table** (`GetUnicastIpAddressTable()`, one
row per address) and then described one at a time with `GetIfEntry2()`. The table is the only API
that reports per-interface addresses and it cannot be filtered by interface, so it is queried once
and only the rows of the interfaces that pass the filter below are read; rows of one interface are
not adjacent, so each interface is handled at its first row. The per-interface call is deliberate —
`GetIfTable2()` marshals every registered adapter and costs about 0.95 ms on a machine with a few
dozen of them, while `GetIfEntry2()` costs about 24 µs each, which wins as long as fewer than
roughly 40 interfaces carry an address.

Because the enumeration starts from the address table, an interface that carries no unicast address
is never seen — it cannot be listed even when only `showMac` is on.

The filters run in this order: the default route (`defaultRouteOnly`, both lookups cached, checked
before `GetIfEntry2()` so uninteresting interfaces are not queried at all), then `OperStatus` must be
`IfOperStatusUp`, then loopback (`IF_TYPE_SOFTWARE_LOOPBACK`) is dropped unless `showLoop` is set,
then `namePrefix` against the adapter alias.

`flags` is built from `MIB_IF_ROW2` rather than from the `IP_ADAPTER_*` bits: the
`InterfaceAndOperStatusFlags` members (`HARDWARE`, `FILTER`, `CONNECTOR_PRESENT`,
`NOT_AUTHENTICATED`, `NO_MEDIA`, `PAUSED`, `LOW_POWER`, `ENDPOINT`), the direction (`SEND_ONLY` /
`RECEIVE_ONLY` / `SEND_RECEIVE`), and two enums that report exactly one value per interface and
therefore always contribute a flag — `MediaConnectState` (`MEDIA_UNKNOWN` / `MEDIA_CONNECTED` /
`MEDIA_DISCONNECTED`) and the IANA interface type from `ipifcons.h` (`ETHERNET_CSMACD`,
`IEEE80211`, `SOFTWARE_LOOPBACK`, `TUNNEL`, …), which is what the CHANGELOG's `ETHERNET_CSMACD` /
`IEEE80211` / `SOFTWARE_LOOPBACK` examples refer to.

IPv4 addresses come from `RtlIpv4AddressToStringA`, IPv6 from `RtlIpv6AddressToStringA`, and the `/N`
suffix is appended from `OnLinkPrefixLength`. Unless `showAllIps` is set, only addresses whose
`DadState` is `IpDadStatePreferred` and whose `SuffixOrigin` is not `IpSuffixOriginRandom` are used,
which is what keeps temporary IPv6 addresses out. `speed` is `ReceiveLinkSpeed / 1000000`, `mtu` is
the adapter's `Mtu`, and `mac` is formatted from the 6-byte `PhysicalAddress` (adapters with another
length get no MAC).

### Linux / macOS / BSD / Solaris / Haiku / GNU

`getifaddrs()` gives one entry per address. Entries are grouped by interface name into `FFAdapter`
records, and an interface is only turned into a result when it has at least one IPv4/IPv6 address (or
`showMac` is on and a link-layer entry was seen). Interfaces that are not `IFF_RUNNING` are skipped,
loopback is skipped unless `showLoop` is set, and only the interfaces that own the default route
survive `defaultRouteOnly`.

The address family of the chosen entry decides how the address is rendered: `AF_INET` / `AF_INET6`
through `inet_ntop()`, `AF_LINK` (macOS, BSDs) or `AF_PACKET` (Linux) for the MAC. The prefix length
is the population count of the netmask. When `defaultRouteOnly` is on, the IPv4 address is further
narrowed to the default route's `preferredSourceAddrV4` if that address is present, falling back to
the first IPv4 address otherwise.

MTU and speed need a `SOCK_DGRAM` socket: `SIOCGIFMTU` for the MTU, `SIOCETHTOOL` (Linux) or
`SIOCGIFMEDIA` (macOS/BSD, mapping the media subtype to a speed) for the link speed, and the `link`
kstat on Solaris. On Linux the IPv6 address kind is classified from the address prefix first
(`IN6_IS_ADDR_GLOBAL` / `UNIQUE_LOCAL` / `LINKLOCAL`) and then refined through `SIOCGIFAFLAG_IN6`
(or, on Linux, `/proc/net/if_inet6`) to mark deprecated, temporary and tentative addresses as
secondary so the "most useful" address can be chosen.

### macOS: the redacted MAC address

`getifaddrs()` reports `02:00:00:00:00:00` as the link-layer address of every interface when the
process is not signed as an Apple platform binary, so `ffLocalIpFixRedactedMacs()`
(`localip_apple.c`) walks the IORegistry for the interfaces whose MAC came back as that exact
placeholder — and does nothing at all when none did. It enumerates `IONetworkInterface` and
`IOUserNetworkWLAN`, since the dext-based Wi-Fi interfaces are not subclasses of the former, matches
on `BSD Name`, and looks for `IOMACAddress` on the node or one of its first four ancestors: the depth
depends on the driver (`en0` carries it on `IO80211Controller`, `en4` on `AppleUSBDeviceNCM11Data`,
`ap1` on the node itself). Interfaces the enumeration does not reach are retried individually with
`IOBSDNameMatching()`, so the pass can only ever be faster than, never worse than, looking every
interface up by name — measured at 1.5 ms against 3.0 ms for the 21 interfaces of a typical Mac.

`SystemConfiguration` is the obvious alternative and was rejected: `SCNetworkInterfaceCopyAll()` is
an XPC round trip to `configd` costing 2.6–5.2 ms, and it does not list `anpi0`–`anpi2` or `ap1` at
all. The whole replacement measured a p50 of 1.5 ms against 0.1 ms for the same run without the
placeholders.
