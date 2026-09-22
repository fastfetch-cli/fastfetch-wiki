# NetIO

> Print network I/O throughput

| | |
|---|---|
| Module type | `netio` |
| Default order | 67 (only used by `--gen-config`) |
| Module source | `src/modules/netio/netio.c` |
| Detection source | `src/detection/netio/` |

Prints one line per network interface with the data received and sent since the previous snapshot,
divided by the elapsed time — a rate in bytes per second. By default only the interface that carries
the default route is shown. The key is `Network I/O (<interface name>)`.

```
Network I/O (en0): 944 B/s (IN) - 849 B/s (OUT)
Network I/O (lo0): 361 B/s (IN) - 361 B/s (OUT)
```

`(IN)` is received data and `(OUT)` is sent data. When `defaultRouteOnly` is turned off, the
interface that carries the default route is marked with a trailing ` *`. With `detectTotal: true` the
module prints the cumulative counters since boot and the `/s` suffix disappears.

Like `Modules/DiskIO`, this module needs two readings to compute a rate and therefore **waits**
`waitTime` milliseconds (250 by default) before it can print anything. `detectTotal: true` does not
wait.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `netio_linux.c` | `/sys/class/net/*/statistics`, honouring `operstate` |
| Android | `netio_linux.c` | Same file |
| GNU/Hurd | `netio_nosupport.c` | Reports "Not supported on this platform" |
| FreeBSD | `netio_bsd.c` | `sysctl(NET_RT_IFLIST)` |
| NetBSD | `netio_bsd.c` | Same file |
| OpenBSD | `netio_bsd.c` | Same file |
| Solaris / illumos | `netio_sunos.c` | kstat chain, class `net`, module `link` |
| Haiku | `netio_haiku.cpp` | `BNetworkRoster` plus `GetStats()` |
| macOS | `netio_apple.c` | `sysctl(IFMIB_IFDATA)` |
| Windows | `netio_windows.c` | `GetAdaptersAddresses()` plus `GetIfEntry2()` |

GNU/Hurd is the only platform without an implementation.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `namePrefix` | string | empty | Only show interfaces whose name starts with this string |
| `defaultRouteOnly` | boolean | `true` (`false` on Android) | Show only the interface used for the default route |
| `detectTotal` | boolean | `false` | Print cumulative counters instead of a per-second rate |
| `waitTime` | integer | `250` | Milliseconds between the two counter readings. Ignored when `detectTotal` is true. |
| `key` | string | `Network I/O (<ifname>)` | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

`waitTime` accepts `0`…`4294967295`, but `0` is silently raised to `1`; anything outside the range —
including a negative number — is rejected with a message instead of being reinterpreted.

The Android default for `defaultRouteOnly` is `false` because the interface that the option filters
against is not available through the same API there; on every other platform the option behaves as
documented.

## Format string

Run `fastfetch -h netio-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{ifname}` | Interface name — also available in key format |
| `{rx-size}` | Received bytes, formatted with `display.size`, plus `/s` unless `detectTotal` |
| `{tx-size}` | Sent bytes, formatted the same way |
| `{rx-bytes}` | Bytes received per second, as a raw number |
| `{tx-bytes}` | Bytes sent per second, as a raw number |
| `{rx-packets}` | Packets received per second |
| `{tx-packets}` | Packets sent per second |
| `{rx-errors}` | Receive errors per second |
| `{tx-errors}` | Send errors per second |
| `{rx-drops}` | Packets dropped while receiving, per second |
| `{tx-drops}` | Packets dropped while sending, per second |
| `{is-default-route}` | `true` when the interface carries the default route |

The `…-bytes`, `…-packets`, `…-errors` and `…-drops` values are already rates; only `{rx-size}` and
`{tx-size}` carry a `/s` suffix of their own. `{is-default-route}` is about the interface, not about
the current configuration — it is `true` for the default route even when `defaultRouteOnly` is on and
the ` *` marker is suppressed.

## JSON output

```jsonc
{
    "type": "NetIO",
    "result": [
        {
            "name": "en0",
            "defaultRoute": true,
            "txBytes": 0,
            "rxBytes": 262,
            "txPackets": 0,
            "rxPackets": 3,
            "rxErrors": 0,
            "txErrors": 0,
            "rxDrops": 0,
            "txDrops": 0
        }
    ]
}
```

The ten numeric fields are per-second rates unless `detectTotal` is set, in which case they are the
raw cumulative counters. On failure the object is `{ "type": "NetIO", "error": "…" }`.

## Examples

```jsonc
// Every interface, with the default route marked
{ "type": "netio", "defaultRouteOnly": false }
```

```jsonc
// Cumulative counters, no waiting
{ "type": "netio", "detectTotal": true }
```

```jsonc
// A Wi-Fi-only line, refreshed over one second
{ "type": "netio", "namePrefix": "wlan", "defaultRouteOnly": false, "waitTime": 1000 }
```

```jsonc
// Compact upload/download line
{ "type": "netio", "format": "IN {rx-size} OUT {tx-size}" }
```

## Pitfalls

- **The module blocks for `waitTime` milliseconds.** The default 250 ms is paid by every run that
  uses the rate path, including `--format json`. A larger value really does block — `4294967295` is
  about 49.7 days and is accepted without a warning — so a mistyped `waitTime` looks like a hang.
- **Only one interface is shown by default.** `defaultRouteOnly` is `true`, which is why a laptop
  prints a single line even though a dozen interfaces exist. Turn it off to see the rest; the default
  route is then marked with ` *`.
- **The ` *` marker and `{is-default-route}` do not agree in the default configuration.** The marker
  is suppressed whenever `defaultRouteOnly` is on, while the variable keeps reporting `true`; a format
  that renders both shows the variable but no star.
- **Linux hides interfaces that are not up.** Anything whose `operstate` is not `up` or `unknown` is
  skipped, so a Wi-Fi interface that is associated but administratively down, or a USB Ethernet
  adapter with no link, is missing from the list rather than reported as zero. macOS, FreeBSD, NetBSD
  and OpenBSD filter on `IFF_RUNNING` and skip `IFF_NOARP` interfaces instead.
- **Windows does not filter at all.** Every adapter returned by `GetAdaptersAddresses()` is listed —
  disconnected Wi-Fi, VPN tunnels, Hyper-V switches — when `defaultRouteOnly` is off, so the interface
  count depends much more on the platform than on the machine.
- **Two snapshots can disagree.** An interface appearing or disappearing between them aborts the
  module with `Different number of network interfaces. Network change?`; a renamed interface aborts it
  with `Network interface name changed`. Both messages only appear when `display.showErrors` is true.
- **`namePrefix` is matched against the platform's own interface naming.** On Linux and Solaris that
  is the kernel name (`eth0`, `en0`, `net0`), on macOS the BSD name, and on Windows the adapters'
  friendly name (`Ethernet`, `Wi-Fi`) — so the same value does not port between platforms. A prefix
  that matches nothing leaves the module with an empty list, and it then reports
  `No network interfaces found`.
- **`{tx-drops}` is always zero on Solaris and on older BSDs.** The Solaris kstat interface has no
  drop counter, and BSD builds only read `ifi_oqdrops` when the platform provides it; the field is
  filled with `0` rather than left out of the JSON result.
- **The JSON array is not sorted, the text output is.** The sort by name happens while printing, so
  `--format json` returns the detection order — `readdir()` order for Linux, the sysctl order for
  macOS and the BSDs.
- **Under `--dynamic-interval` the default route is re-resolved every round.** The netif layer that
  supplies it is invalidated at each round boundary, so moving between Wi-Fi and Ethernet makes the
  reported interface follow — and because the module then sees a different interface set, a round can
  fail with "Different number of network interfaces" instead of printing a rate.
- **Every option warning is printed twice when the module is configured from a file.** The module
  object is parsed once in the prepare pass and once while printing, and the parser reports during
  both: a config with one unknown key and one out-of-range `waitTime` produces four lines, not two.
  The same doubling happens with `-s netio` as long as a config supplies the options.

## Implementation

Every backend implements `ffNetIOGetIoCounters()`. Rates are computed in the shared
`src/detection/netio/netio.c`: `ffPrepareNetIO()` stores the first snapshot in a file-scope static,
`ffDetectNetIO()` takes the second after sleeping until `waitTime` has elapsed and rewrites each
counter as `(current − previous) × 1000 / elapsed_ms`, keeping the raw value as the next baseline.
The eight `uint64_t` counters are walked in one loop, guarded by a `static_assert` on the struct
layout. The snapshots are matched positionally, which is why a changed interface count or a renamed
interface is an error rather than a skipped entry.

All ten backends call `ffNetifGetDefaultRouteV4()` first: with `defaultRouteOnly` they fetch only
that interface, and without it they use the result to set the flag behind ` *` and
`{is-default-route}`.

### Linux and Android

`/sys/class/net/` is walked and each interface is read through directory descriptors: `operstate`
gates the entry, then `statistics/rx_bytes`, `tx_bytes`, `rx_packets`, `tx_packets`, `rx_errors`,
`tx_errors`, `rx_dropped` and `tx_dropped` are parsed. Only `up` and `unknown` pass the gate —
`unknown` is how the loopback interface reports itself, which is why `lo` is listed — while
`down`, `lowerlayerdown` and `dormant` interfaces are dropped.

### FreeBSD, NetBSD, OpenBSD and macOS

`sysctl(NET_RT_IFLIST)` with the default route's interface index when `defaultRouteOnly` is set, and
with `0` otherwise. The `if_msghdr` chain is walked, skipping anything that is not `RTM_IFINFO`, not
`IFF_RUNNING` or flagged `IFF_NOARP`; the interface name comes from the following `sockaddr_dl`. The
counters are `ifi_ibytes`/`ifi_obytes`, `ifi_ipackets`/`ifi_opackets`,
`ifi_ierrors`/`ifi_oerrors` and the drop counters, with `txDrops` only available when the build has
`ifi_oqdrops`.

macOS uses the same mib through the `if_mib` layer (`IFMIB_IFDATA` for one interface, `IFMIB_IFALLDATA`
for all of them) and reads `ifmibdata` records, including `ifmd_snd_drops` for the send-side drops.

### Solaris and illumos

The kstat chain is walked for entries whose class is `net` and whose module is `link`; the name is the
kstat name and the default route is compared by name. `obytes64`, `rbytes64`, `opackets64`,
`ipackets64`, `oerrors` and `ierrors` are looked up with `kstat_data_lookup()`, and both drop counters
are set to `0` because the interface has no such statistic.

### Haiku

`BNetworkRoster::Default()` enumerates the interfaces, `interface.Exists()` filters the unused ones
and `GetStats()` fills an `ifreq_stats` with `send`/`receive` byte, packet, error and drop counters.
The default route is identified by interface index.

### Windows

`GetAdaptersAddresses()` is called in a retry loop (up to four times, growing the buffer on
`ERROR_BUFFER_OVERFLOW`) with anycast, multicast and DNS lookups skipped. The friendly name is
converted from UTF-16 and `GetIfEntry2()` supplies the `MIB_IF_ROW2` counters: `InOctets`/`OutOctets`,
unicast plus non-unicast packets, `InErrors`/`OutErrors` and `InDiscards`/`OutDiscards`. Unlike the
BSD path there is no link-state filter, so adapters with no link are listed with counters of their
own.
