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
| Windows | `localip_windows.c` | `GetAdaptersAddresses()` |
| macOS | `localip_linux.c` | `getifaddrs()`; speed via `SIOCGIFMEDIA` |
| FreeBSD / OpenBSD / NetBSD / DragonFly | `localip_linux.c` | `getifaddrs()`; speed via `SIOCGIFMEDIA` |
| Solaris / illumos | `localip_linux.c` | `getifaddrs()`; speed from the `link` kstat |
| Haiku | `localip_linux.c` | `getifaddrs()` |
| GNU/Hurd | `localip_linux.c` | `getifaddrs()`; MAC via `SIOCGIFHWADDR` |
| Android | `localip_linux.c` | Same code; `defaultRouteOnly` is off by default (permission denied) |

Only Windows has its own backend — every other platform shares `localip_linux.c`.

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
  the sentinel `-1` for `mtu` and `0` for `speed`.

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

## Implementation

`ffDetectLocalIps()` fills a list of `FFLocalIpResult` (five `FFstrbuf`s — `name`, `ipv4`, `ipv6`,
`mac`, `flags` — plus `mtu`, `speed` and a `defaultRoute` bit field) and returns an error string.
`ffPrintLocalIp()` and `ffGenerateLocalIpJsonResult()` each call it themselves, so a
`--dynamic-interval` run re-probes the interfaces every round; nothing is cached.

The default `showType` is IPv4 | prefix length | default-route-only. On Windows that last bit is
skipped for Android, where the default route is only available through a restricted syscall.

### Windows

`GetAdaptersAddresses()` is called with `AF_UNSPEC`, `AF_INET` or `AF_INET6` depending on which
families are enabled, and with `GAA_FLAG_SKIP_ANYCAST | SKIP_MULTICAST | SKIP_DNS_SERVER`. The call
is retried up to five times while it returns `ERROR_BUFFER_OVERFLOW`, growing the buffer each time.

Only adapters whose `OperStatus` is `IfOperStatusUp` are kept; the loopback adapter
(`IF_TYPE_SOFTWARE_LOOPBACK`) is dropped unless `showLoop` is set, and `namePrefix` and the
default-route filter are applied before anything else. IPv4 addresses come from `FirstUnicastAddress`
with `RtlIpv4AddressToStringA`, IPv6 with `RtlIpv6AddressToStringA`, and the `/N` suffix is appended
from `OnLinkPrefixLength`. Unless `showAllIps` is set, only addresses whose `DadState` is
`IpDadStatePreferred` and whose `SuffixOrigin` is not `IpSuffixOriginRandom` are used, which is what
keeps temporary IPv6 addresses out. `speed` is `ReceiveLinkSpeed / 1000000`, `mtu` is the adapter's
`Mtu`, `mac` is formatted from the 6-byte `PhysicalAddress` (adapters with another length get no MAC),
and the flags string is built from the `IP_ADAPTER_*` bits listed in the module.

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
