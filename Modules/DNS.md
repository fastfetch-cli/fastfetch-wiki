# DNS

> Print configured DNS servers

| | |
|---|---|
| Module type | `dns` |
| Default order | 51 (only used by `--gen-config`) |
| Module source | `src/modules/dns/dns.c` |
| Detection source | `src/detection/dns/` |

Prints the resolvers the system is configured to use, on one line, IPv4 addresses first and IPv6
addresses after them, separated by single spaces:

```
DNS: 223.5.5.5 8.8.8.8 fe80::1 fe80::1
```

The two groups are decided by the characters in each entry, not by a parsed address family: an entry
that contains a `.` goes into the IPv4 group, an entry that contains a `:` goes into the IPv6 group.
The `result` string is always on one line, so a machine with many resolvers produces a long line
rather than several.

An empty list is an error on the print path (`No DNS servers detected`) and an empty array on the
JSON path — see Pitfalls.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `dns_linux.c` | `/etc/resolv.conf` plus the usual alternative paths |
| Android | `dns_linux.c` | Same code, without the systemd-resolved / NetworkManager follow-ups |
| FreeBSD | `dns_linux.c` | `/etc/resolv.conf` plus BSD-specific fallbacks |
| NetBSD | `dns_linux.c` | Same code |
| OpenBSD | `dns_linux.c` | Same code |
| Solaris / illumos | `dns_linux.c` | Same code |
| Haiku | `dns_linux.c` | Reads `/system/settings/network/resolv.conf` |
| GNU/Hurd | `dns_linux.c` | Same code as Linux |
| macOS | `dns_apple.c` | `SystemConfiguration`, falling back to `/var/run/resolv.conf` |
| Windows | `dns_windows.c` | `GetAdaptersAddresses()` |

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `showType` | `both` \| `ipv4` \| `ipv6` | `both` | Which families to collect |
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

`showType` filters inside the detector, so the unwanted family never reaches the result list — it is
not filtered afterwards. Setting it to an unknown value reports
`DNS: Invalid showType value: Invalid enum string` (visible only with `display.showErrors: true`) and
then continues with the previous value, so a typo leaves the module printing both families.

## Format string

Run `fastfetch -h dns-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{result}` | All collected resolvers, already joined by spaces, IPv4 group first |

`{result}` is the finished line, not a list, so there is no way to iterate or index the resolvers
from a format string. It is not available in the key format either — `ffPrintLogoAndKey()` parses
`key` with `{index}`, `{icon}` and `{module-name}` only.

Two details of the shared format engine are easy to get wrong:

- **An unknown placeholder is copied verbatim, conditional syntax included.** `"key": "{?result}x{?}"`
  prints the text as written, because `result` does not exist in the key namespace.
- **A conditional treats an empty string as unset.** `formatArgSet()` counts a string argument as set
  only when its length is non-zero, so `{?result}…{?}` is equivalent to a plain `{result}` here (the
  detector never returns success with an empty list on the print path).

## JSON output

```jsonc
[
    {
        "type": "DNS",
        "result": [
            "fe80::1",
            "223.5.5.5",
            "8.8.8.8",
            "fe80::1"
        ]
    }
]
```

`result` is an array of strings **in detection order** — the reordering and joining the text path
does is not applied here (the array above is the same machine as the sample line at the top of the
page). Duplicate addresses are kept: each adapter contributes its own entry, so a link-local
resolver appears once per interface that has it. A detector error produces
`{ "type": "DNS", "error": "…" }` with no `result`.

## Examples

```jsonc
// IPv4 only
{ "type": "dns", "showType": "ipv4" }
```

```jsonc
// Replace the key, and put the resolvers behind a label of your own
{ "type": "dns", "key": "resolv", "format": "servers: {result}" }
```

```jsonc
// One resolver per line is not expressible: {result} is a single string.
// Split it yourself if you need that:
{ "type": "dns", "key": " " }
```

## Pitfalls

- **The text output reorders, the JSON output does not.** The print path walks the list twice — once
  for entries containing `.`, once for entries containing `:` — so the line always reads
  `IPv4 … IPv6 …`, while `--format json` writes the array exactly as the detector produced it. On the
  machine used for the samples above the text starts with `223.5.5.5` and the JSON array starts with
  `fe80::1`. A script that compares the two will conclude the resolvers changed when they did not.
- **A resolver that is neither dotted nor coloned disappears from the text output but stays in the
  JSON.** `nameserver` accepts a hostname, and the Linux/macOS parsers keep it (their filter only
  drops entries that have a `:` while IPv6 is off, or a `.` while IPv4 is off). The print path then
  drops it, because it matches neither of the two selection loops. The JSON array still contains it.
- **An IPv4-mapped IPv6 address is printed twice.** `::ffff:192.0.2.1` contains both a `.` and a `:`,
  so it passes the filter in both selection loops and is appended to the line twice. It also survives
  the `showType` filter in either direction, because `showType: "ipv4"` only drops entries containing
  a `:` when IPv6 was *not* requested — which is the case here, so this one address is kept while a
  normal IPv6 address is dropped.
- **An empty result is an error in text and an empty array in JSON.** The print path reports
  `No DNS servers detected` through `ffPrintError()` (silent unless `display.showErrors` is on, so
  normally the module simply vanishes), while the JSON path returns success with `"result": []` and
  no `error` field. A JSON consumer cannot tell "no resolvers configured" from "this machine has
  none right now".
- **On Windows only the default-route adapter is inspected.** `dns_windows.c` compares each
  adapter's `IfIndex` against `ffNetifGetDefaultRouteV4()->ifIndex` and `break`s after the first
  match, so a resolver configured on a second adapter (a VPN, a Hyper-V or WSL virtual switch, a
  Docker bridge) is invisible. The comparison always uses the **IPv4** default route, even when
  `showType: "ipv6"` was requested and `GetAdaptersAddresses()` was asked for `AF_INET6` — a machine
  with no IPv4 default route yields `ifIndex == 0`, nothing matches, and the module reports an empty
  list although IPv6 resolvers are configured.
- **On Linux a missing `/etc/resolv.conf` aborts the whole detection.** The error from the first
  `fopen()` is returned immediately, so the systemd-resolved, NetworkManager, resolvconf, dnsmasq and
  openresolv follow-up paths are never tried. A container or netns without that file reports
  `fopen(path, r) failed` instead of reading the resolver configuration that does exist.
- **On Linux the follow-up paths only fire in one narrow shape.** The systemd-resolved
  (`/run/systemd/resolve/resolv.conf`) and NetworkManager (`/var/run/NetworkManager/resolv.conf`)
  files are only consulted when the result list contains **exactly one** entry whose value is
  `127.0.0.53` respectively `127.0.0.1`; the resolvconf/dnsmasq/openresolv fallbacks only fire when
  the list is empty. A stub resolver listed next to a real resolver therefore keeps the stub.
- **On macOS `ffDetectDNS()` never fails from the framework path.** When the dynamic store or the key
  cannot be read, the function silently falls back to `/var/run/resolv.conf` and reports whatever
  that returns — including its `fopen(path, r) failed` string, which is then indistinguishable from
  a genuinely unreadable file.
- **DNS is re-detected on every `--dynamic-interval` round.** The resolver list itself is not cached,
  and the netif default route the Windows backend depends on is in the `FFcache` layer, whose entries
  are dropped at each round boundary. A DHCP lease change therefore shows up in the next round
  without restarting fastfetch.
- **`ffPrintError()` writes to stdout.** With `display.showErrors: true` the
  `Invalid showType value: …` line is printed before the JSON array, so the run is no longer
  parseable. Keep `showErrors` off in anything that pipes the JSON output.

## Implementation

`ffPrintDNS()` and `ffGenerateDNSJsonResult()` both call `ffDetectDNS(options, results)` and then
format the same `FFlist` of `FFstrbuf` differently — the print path reorders and joins, the JSON path
skips the reordering. Both destroy the strings afterwards.

### Windows

`GetAdaptersAddresses()` is called with `GAA_FLAG_SKIP_UNICAST | GAA_FLAG_SKIP_ANYCAST |
GAA_FLAG_SKIP_MULTICAST | GAA_FLAG_SKIP_FRIENDLY_NAME`, and the address family follows `showType`
(`AF_INET`, `AF_INET6`, or `AF_UNSPEC` for `both`). The buffer is grown on `ERROR_BUFFER_OVERFLOW`
for up to four extra attempts; any other error returns `GetAdaptersAddresses() failed`. For the
default-route adapter that is `IfOperStatusUp`, every `FirstDnsServerAddress` entry is converted with
`RtlIpv4AddressToStringA()` / `RtlIpv6AddressToStringA()` — an entry of any other family is a gap in
the initialisation described in the project's defect list.

### Linux, Android, FreeBSD, NetBSD, OpenBSD, Solaris, GNU/Hurd, Haiku

`nameserver` lines are read with `getline()` from `resolv.conf` under `FASTFETCH_TARGET_DIR_ROOT`,
with a `#` comment stripped and trailing whitespace trimmed. Entries are kept unless they contain a
`:` while IPv6 is off, or a `.` while IPv4 is off. Everything is relative to
`FASTFETCH_TARGET_DIR_ROOT`, so `--chroot` / a target root actually relocates the lookup.

The platform-specific follow-ups then run (compiled out on Android, which has neither service):
systemd-resolved and NetworkManager when the single entry matches, and the resolvconf / dnsmasq /
openresolv paths (`/run/resolvconf/resolv.conf`, `/var/run/dnsmasq/resolv.conf`,
`/etc/resolv.conf.openresolv`) when the list is empty. FreeBSD, DragonFly, NetBSD and OpenBSD
additionally try `/var/run/resolvconf/resolv.conf`, `/var/run/nameserver` and `/etc/nameserver`.
Reading a follow-up file **clears** the list first, so the earlier entries are not merged into the
later ones.

### macOS

`SCDynamicStoreCreate()` plus the global `DNS` entity
(`SCDynamicStoreKeyCreateNetworkGlobalEntity(…, kSCEntNetDNS)`) provides
`kSCPropNetDNSServerAddresses`, which is filtered by the same `.`/`:` rule as the Linux parser. If
the list is still empty afterwards, `/var/run/resolv.conf` is parsed with the same
`nameserver`-line reader.
