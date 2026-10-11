Fastfetch reads the Android system services directly — mostly over `/dev/binder`, sometimes by
reading a system property — so what a module can see depends on the access held by the UID that runs
it rather than on the app that started it. This page collects what the Android backends need, and
what a user can do about it.

## At a glance

| Module | Android route | What it needs |
|---|---|---|
| Media | `media_session` over `/dev/binder` | an enabled notification listener on the calling UID, and a request that names the caller's own package; the shell UID and root are admitted either way |
| Wifi | `wifi` over `/dev/binder` | `android.permission.ACCESS_WIFI_STATE` on the calling UID, without which the module cannot run at all, and `android.permission.ACCESS_FINE_LOCATION` for the SSID and the BSSID |
| Wallpaper | `wallpaper` over `/dev/binder` | a request that names the caller's own package |
| Battery | `batteryproperties` and `batterystats` over `/dev/binder`, the `debug.tracing.*` properties, `/sys/class/thermal` | nothing; `dumpsys battery`, which is used first for the shell UID and root, needs `android.permission.DUMP` |
| Display / Monitor | `display` over `/dev/binder` | nothing |
| Sound | `audio` over `/dev/binder` | nothing |
| Media and Terminal names | `package` over `/dev/binder`, then the APK's resource table | nothing |

No other Android backend consults a system service that checks its caller. The routes that used to
require the shell UID — `dumpsys display`, `cmd display get-displays` and `dumpsys media_session` —
have been removed rather than kept as fallbacks, so a module that used to answer only for root now
answers for any UID.

## Media sessions: notification access

`ISessionManager.getSessions()` is the one call in the media path that checks a permission.
`MediaSessionService.enforceMediaPermissions()` admits the system UI, a holder of
`android.permission.MEDIA_CONTENT_CONTROL` — a `signature|privileged` permission that cannot be
granted to an ordinary app — a UID that owns an **enabled notification listener**, and the shell UID
and root. Everything else is answered with an `EX_SECURITY` exception. The module asks anyway, because
the answer belongs to the service rather than to the client: a ROM that loosens the check works
without a change here.

Notification access is the one of the four a user can grant, and the only one a Termux install
normally has a use for. An app has to declare a service guarded by
`android.permission.BIND_NOTIFICATION_LISTENER_SERVICE`, and the user then enables it under
*Settings → Apps → Special app access → Notification access*; the enabled set is kept in the
`enabled_notification_listeners` entry of `Settings.Secure`. The Termux app declares one, which is how
a Termux UID reads the session list once notification access has been granted to it.

The request also has to name a package, because the service rejects a component whose package the
calling UID does not own — `packageName does not belong to the calling uid` — before it reaches the
permission check at all. The module therefore declares its own package and leaves the class name
empty, since a standalone binary has no component of its own to name. The class itself is never
validated, so an invented one would be a claim that cannot be backed.

A null component is not equivalent: the notification-listener branch is reached through
`isEnabledNotificationListener(compName, uid)`, which answers `false` for a null name by construction.
A null request therefore asks for every session only on behalf of a UID that was going to be admitted
anyway — which is why the module sends a named one.

When the service declines, the message names the case: `Reading the media session from an app UID
needs an enabled notification listener, which this UID does not have`, or `The media session service
refused the request` for the shell and root.

## Wi-Fi: one permission to run, another for the names

`IWifiManager.getConnectionInfo()` needs `android.permission.ACCESS_WIFI_STATE`. It is a normal
permission, so the user is never prompted for it, but it is granted to a **UID** rather than to a
package: the service checks the caller's UID, and a UID holds the permission as soon as any one of the
packages sharing it requests it.

That is why this module needs no `termux-api` command. `com.termux` requests no Wi-Fi permission at
all, while Termux:API does and shares `sharedUserId com.termux`, so the permission lands on the app's
own UID. Removing Termux:API takes it off the UID again and the module then reports
`The Wifi service raised an exception` where it used to report the connection; reinstalling it brings
the connection back. Without that permission there is no degraded answer to give: the first call is
refused, so the module cannot run rather than run worse. The shell and root UIDs are not affected.

Holding it is not the same as being told everything. The names the caller may not see are removed
before the reply leaves the service — the SSID is dropped, `02:00:00:00:00:00` is written in place of
the BSSID and the MAC address, and the network id is rewritten to `-1` — while the signal, the rates,
the frequency, the IPv4 address and the supplicant state are carried over untouched. That copy is made
for every caller the scan-results check refuses, and the check asks for **`ACCESS_FINE_LOCATION`**:
location access is a runtime permission the user can revoke at any time, so a caller that once read the
SSID keeps its Wi-Fi permission and stops being told the name. Nothing else about the reply moves.

The module reports the two withheld names as `<redacted>` rather than as nothing, because an empty
SSID is what it prints the interface state in place of, and the reply still carries a signal, a
channel and a rate worth showing. They are the two fields a lapsed location permission takes away and
nothing else; they stay empty when there is no connection at all, where an absent name is the truth
rather than a redaction. On the device this was written on the Termux UID does reach the check and
reads both names, so what it prints there is the real network name — the `<redacted>` path is the one
a revoked location permission lands on.

## Wallpaper: naming yourself is the check

`IWallpaperManager.getWallpaper(String callingPackage, int which, int userId)` answers only when that
name is the caller's own. The shorter overload — the one that passes a null name — comes back as a
`SecurityException` raised by `StorageManager.checkPermissionReadImages()`, and an empty string, an
unknown package or a real package belonging to a different UID all leave with no file descriptor. That
`READ_MEDIA_IMAGES` denial is not something a Termux-style app can lift, so naming the caller is the
only route.

The wallpaper itself is out of reach either way: `/data/system/users/<userId>` is mode `0700` and owned
by `system`, and `open`, `stat` and `ls` all answer `EACCES` to an app UID. What the module reports is
the target of a descriptor the service opens inside `system_server` and hands back.

## Battery: `dumpsys` needs DUMP, the app path does not

`dumpsys battery` is behind `android.permission.DUMP`, which only the shell UID and root hold: an app
UID is answered with `Can't find service: battery` on stdout and a zero exit status, so forking it
there costs a child process and cannot succeed. It is still tried first where it is allowed to run,
because it is the richer of the two routes and is the only one that carries the technology and the
critical capacity level. Everywhere else the module reads the battery properties registrar over
`/dev/binder`, the two `debug.tracing.*` properties the battery service mirrors out of each update,
and the kernel's thermal zones, none of which needs a permission.

## Reading an app's display name

The `media` module prints the name of the app that is playing — `player.name` in its JSON — and the
`terminal` module names the Termux app itself. Both come from one lookup: `IPackageManager.getApplicationInfo`
for the `sourceDir` and the `labelRes`, then the APK's own `resources.arsc`, which the platform keeps
**stored rather than deflated** so that it can be mapped without inflating it. The label is a string in
an app's resource table, so nothing here needs a permission; measured on the device this was written
on, the reply also came back for packages the caller does not own.

The lookup costs about half a millisecond and is only paid by the module that needs it. `media` asks
once, and only after a session has been picked; `terminal` asks only when the process tree names
`com.termux`. When the label cannot be read — the app declares a literal string instead of a resource,
or its table is deflated — both fall back to the package name.

## Giving fastfetch a package name

Two services — media and wallpaper — check the caller by name, so the module has to be able to say
which package it is running as. That name is derived from `/proc/self/exe`: a binary inside
`/data/data/<package>/...` belongs to `<package>`, and the shell UID falls back to `com.android.shell`.
A binary that lives outside an app data directory has no package name at all, and its callers then fall
back to what they did before — media sends a null component, and the wallpaper module reports
`Cannot determine the package name of this process`.

## Running as shell or root

The shell UID and root hold `android.permission.DUMP` and `android.permission.MEDIA_CONTENT_CONTROL`,
so none of the gates above applies to them: `dumpsys battery` is used first, the media session list is
answered for any component shape, and the wallpaper's fallback name is `com.android.shell`. Nothing
requires that, and running as an app UID only narrows the answer in two places — the battery's
technology and critical capacity level, and the media session list on a device where notification
access has not been granted.
