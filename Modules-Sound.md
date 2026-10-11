# Sound

> Print sound devices, volume levels, etc

| | |
|---|---|
| Module type | `sound` |
| Default order | 61 (only used by `--gen-config`) |
| Module source | `src/modules/sound/sound.c` |
| Detection source | `src/detection/sound/` |

Prints one line per matching output device: the name, then the volume.

```
Sound: Speaker (Realtek(R) Audio) (100%)
```

By default only the **main** device is listed, so a single line is the normal result. With more than
one device the key is numbered, and the main device is additionally marked with ` (*)`.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `sound_linux.c` | PulseAudio's own native protocol over the daemon socket; **no other backend and no fallback** |
| Android | `sound_android.c` + `sound_linux.c` | The `audio` (IAudioService) system service over raw binder while SurfaceFlinger is the display server, otherwise PulseAudio. Needs no permission (see [Android Permissions](Android-Permissions)) |
| FreeBSD / MidnightBSD / DragonFly | `sound_bsd.c` | `ioctl(SNDCTL_MIXER_*)` on `/dev/mixer*` |
| NetBSD | `sound_nbsd.c` | `ioctl(AUDIO_GETDEV)` … on `/dev/audio*` |
| OpenBSD | `sound_obsd.c` | `ioctl(AUDIO_GETDEV)` … on `/dev/audio*` |
| Solaris / illumos | `sound_sunos.c` | OSS `ioctl(SNDCTL_SYSINFO)` |
| macOS | `sound_apple.c` | CoreAudio |
| Windows | `sound_windows.cpp` | Core Audio APIs (`IMMDeviceEnumerator`) |
| Haiku | `sound_haiku.cpp` | `BMediaRoster` / the media kit |
| GNU/Hurd | `sound_linux.c` | Same code |

Every platform has an implementation. Which device ends up in the list is decided by `soundType`,
and each backend applies that filter in its own way — see the pitfalls.

Android compiles both files and the display server decides between them, the same arrangement as
`wallpaper` and `media`. SurfaceFlinger means the sound that is audible is Android's own, so
`sound_android.c` asks `IAudioService` over `/dev/binder`; a Termux:X11 or Wayland session runs a real
desktop on top of the device, and what plays there belongs to that desktop, so `sound_linux.c` —
compiled alongside for exactly that reason — answers over PulseAudio. Only one of the two is ever
consulted, never merged.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `soundType` | string | `main` | Which devices to print: `main`, `active` or `all` |
| `percent` | object | `{ "green": 80, "yellow": 90, "type": 0 }` | Colour thresholds and style for the volume |
| `key` | string | `Sound` | Module key. A single space hides the key and the separator |
| `keyColor` | color | – | Overrides `display.color.keys` |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works; `""` prints none. |
| `keyWidth` | integer | – | Overrides `display.key.width` |
| `outputColor` | color | – | Overrides `display.color.output` |
| `format` | string | – | Custom output format (see below) |
| `condition` | object | – | Show the module only if the conditions match |

`soundType` selects a bit mask, and the backends interpret it as "report devices carrying this bit":

- `main` — only the default / main output device. This is the default and normally yields one line.
- `active` — only devices the platform reports as active (plugged in, present, not disabled).
- `all` — no filtering at all, so unplugged and disabled devices are listed too.

`percent` behaves like every other percentage option: `type: 0` means "follow
`display.percentType`", which is `num | num-color` out of the box and produces the `(100%)` suffix.
`bar` adds a bar, `hide-others` removes the volume from the default line entirely.

## Format string

Run `fastfetch -h sound-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{is-main}` | Is main sound device |
| `{is-active}` | Is active sound device |
| `{name}` | Device name |
| `{volume-percentage}` | Volume (in percentage num) |
| `{identifier}` | Identifier |
| `{volume-percentage-bar}` | Volume (in percentage bar) |
| `{platform-api}` | Platform API used |

None of the seven is marked `*` in the help output, so none is available in the key format.

`{is-main}` and `{is-active}` are booleans — they print `true` / `false`, not a number.
`{volume-percentage}` and `{volume-percentage-bar}` are empty when the volume is unknown **and**
when `percent.type` does not contain the corresponding bit, so a format that always wants the number
has to set `percent.type` explicitly. `{platform-api}` is a free-form string chosen by the backend
(`Core Audio APIs` on Windows, for example).

## JSON output

```jsonc
[
    {
        "type": "Sound",
        "result": [
            {
                "name": "Speaker (Realtek(R) Audio)",
                "identifier": "{0.0.0.00000000}.{<guid>}",
                "platformApi": "Core Audio APIs",
                "type": [ "main", "active" ],
                "volume": 100
            }
        ]
    }
]
```

- `result` is always an array. On failure the object carries `error` instead and has no `result`.
- **`type` is an array of strings**, not a single string: it contains `main`, `active`, both, or —
  for a device that is neither — is an empty array `[]`.
- `volume` is a number `0`–`100`, or **`null`** when the platform does not report one. The sentinel
  for "unknown" is `255` internally and is never emitted.
- The key order inside each object is `name`, `identifier`, `platformApi`, `type`, `volume`.
- The JSON never applies `percent` and never uses the custom `format`; `type` in the JSON is the
  device type array, unrelated to the module's own `soundType` option.

## Examples

```jsonc
// Every device, including the disabled ones
{ "type": "sound", "soundType": "all" }
```

```jsonc
// Name and a bar instead of a number
{ "type": "sound", "format": "{name} {volume-percentage-bar}", "percent": { "type": 2 } }
```

```jsonc
// Only the device name, no volume at all
{ "type": "sound", "percent": { "type": ["hide-others"] } }
```

## Pitfalls

- **The ` (*)` marker only appears when several devices are printed.** It is appended when the device
  is main **and** the line index is greater than zero; with a single device the index is `0`, so the
  default `main` mode never shows it. Switching to `all` is what makes it appear.
- **Muting a device means two different things on two platforms.** On Windows the volume is read only
  when `GetMute()` fails or reports *not* muted, so a muted output reports **no volume at all**
  (`null` in the JSON, and no `(…%)` suffix). On Linux a muted sink reports **`0%`**. Neither is
  "unknown" — they are genuinely different values for the same user action.
- **Linux needs a reachable PulseAudio server, and has no fallback.** There is no ALSA path, and no
  library either — the backend speaks PulseAudio's native protocol itself over the daemon socket — so
  a machine running plain ALSA, or one where PulseAudio was replaced by PipeWire with no socket at the
  usual path, reports a connection error and prints nothing.
- **On Android exactly one device is ever reported.** It is the sink the audio policy has selected for
  `USAGE_MEDIA`, taken from the first entry of `getDevicesForAttributesUnprotected`, and not a list of
  connected outputs — so `soundType: "all"` does not widen it.
- **On Android the volume is a stream index, not a percentage.** `getStreamVolume` answers with the
  index the service keeps for `STREAM_MUSIC` (0–150 on one measured device, against 15 for the system
  stream), so the percentage is derived from the minimum and maximum the same service reports. A muted
  stream is reported as `0`, like the PulseAudio backend and unlike Windows.
- **On Android a Bluetooth sink is named by a second call.** The policy reply carries an empty `name`
  and an address the service has partly blanked (`XX:XX:XX:XX:D8:61`), so the readable name comes from
  `getBtActiveDeviceName()`; a build that does not declare it, or a service with no name to give, falls
  back to the type name. That call is not a routing decision — it answers even while the speaker is
  what is in use — so it is only ever consulted after the device list has already picked a Bluetooth
  sink.
- **On Android `active` means "something is playing"**, from `isMusicActive()`. A build that does not
  declare the method, or a service that refuses it, leaves the bit clear rather than failing the
  module.
- **`platformApi` is not a constant across platforms.** Windows always reports `Core Audio APIs`,
  Android always reports `AudioService`, while Linux reports the PulseAudio server name and version,
  so a script cannot treat the field as an enum.
- **`soundType: "active"` does not mean "currently playing".** It maps to the platform's notion of a
  present / enabled device. `all` in turn disables the filter entirely, which is why it also lists
  devices that are not plugged in.
- **`main` is resolved differently per platform.** On Windows the default endpoint is fetched with
  `GetDefaultAudioEndpoint()` and, in `main` mode, the module returns immediately after it — so
  `main` mode can only ever produce one device there. In the other modes the default device's id is
  remembered and whichever endpoint matches it is flagged `main`.
- **Devices that fail individually are skipped silently.** A device whose id or property store cannot
  be read is dropped from the list rather than reported, so a machine with two outputs can show one.
- **The volume is a rounded scalar, not an exact dB value.** It is `masterVolumeLevelScalar * 100`
  rounded to the nearest integer, which is the Windows mixer's own slider value.
- **Errors are invisible by default.** `No matched sound devices found` — the empty-list case — goes
  through `ffPrintError()` and therefore needs `display.showErrors` to be `true` to be seen. The JSON
  path has no empty-list check and emits `{"result": []}`, the same divergence `keyboard` and `mouse`
  have.
- **Older builds reported the module as failed even when it printed.** `ffPrintSound()` used to
  return the initial `false` no matter what, which suppressed every following module gated on
  `condition.succeeded`. This was fixed (B60); it is the only module that ever had the defect.

## Implementation

`ffDetectSound()` appends `FFSoundDevice` values to a list — `identifier`, `name`, `platformApi`, a
`uint8_t volume` and an `FFSoundType` bit mask — and returns an error string.
`ffPrintSound()` and `ffGenerateSoundJsonResult()` each call it and each release the three strings of
every element afterwards; nothing is cached.

`printDevice()` renders one line. With no custom `format` it appends the name (unless
`hide-others` is set), then the bar and/or the number according to `percent.type`, and finally the
` (*)` marker. With a custom `format` the two percentage variants are pre-rendered into strings and
the two type bits are turned into booleans before the format engine sees them.

### Windows

COM is initialised and an `IMMDeviceEnumerator` is created. In `main` mode
`GetDefaultAudioEndpoint(eRender, eMultimedia, …)` supplies the single device and the function
returns. Otherwise the default device's id is kept and `EnumAudioEndpoints()` is called with
`DEVICE_STATE_ACTIVE` plus `DEVICE_STATE_DISABLED` when `active` is not requested; every endpoint of
the collection is then visited. For each one, `type` is built from "id equals the default id" and
"`DEVICE_STATE_ACTIVE` is set", the name comes from `PKEY_Device_FriendlyName` with
`PKEY_Device_DeviceDesc` as a fallback and `Unknown Device` as a last resort, and the volume comes
from `IAudioEndpointVolume::GetMasterVolumeLevelScalar()` unless the endpoint is muted.
`platformApi` is the constant `Core Audio APIs`.

### Linux / GNU/Hurd

There is exactly one backend, and it is not a library client: `sound_linux.c` speaks PulseAudio's
native protocol itself over the daemon socket, so no `libpulse` is linked or loaded at runtime and
there is no build option for one. A PulseAudio server still has to be reachable — a machine running
plain ALSA, or PulseAudio replaced by PipeWire with no socket at the usual path, reports a
connection error rather than falling back.

The server is reached at `$PULSE_SERVER`, or at the socket under `$PULSE_RUNTIME_PATH`, and the two
queries a library client would make are sent as native protocol commands: the server information first
(the server name, with a `(on …)` wrapper stripped, and the default sink name), then the sink list. A
sink is `main` when its name equals the default sink name and `active` when it has an active port whose
availability is not `PA_PORT_AVAILABLE_NO`. In `main` mode the enumeration stops as soon as one device
has been collected, so only the default sink is reported. `platformApi` is the PulseAudio server
string, not a constant, and `identifier` is the sink name. The volume is
`volume.values[0] * 100 / PA_VOLUME_NORM` rounded — and it is forced to **0** when the sink is muted,
which is the opposite of what the Windows backend does.

### Android

`sound_android.c` answers only while SurfaceFlinger is the display server; anything else is forwarded
to the Linux implementation above. It opens `/dev/binder` through `common/android/binder.h` — no child
process, no JNI, no permission — and resolves the `audio` service, i.e. `android.media.IAudioService`,
through the service manager.

Every transaction code is read out of the device's own `framework.jar` at run time, by the name of the
constant the dex carries for it (`TRANSACTION_getStreamVolume` and six others), rather than written
down: the interface declares 283 methods in AOSP and 324 on one measured device, so a vendor that
inserts a method ahead of the one being called shifts it. The seven are asked for in one walk, because
they all sit in the same class of the same dex entry. Five of them — the three volume getters,
`isStreamMute` and `getDevicesForAttributesUnprotected` — are required, and a build that does not
declare one is a message rather than a wrong number; the other two only refine the answer and are
allowed to be missing, because which methods exist differs per device.

The device comes from `getDevicesForAttributesUnprotected`, which is the unprotected twin of
`getDevicesForAttributes` — the protected one, and `getLastAudibleStreamVolume`, are refused for
wanting `MODIFY_AUDIO_ROUTING` or `QUERY_AUDIO_STATE`. Its `AudioAttributes` argument is written by
hand, and two of its fields are not cosmetic: `mSource` has to be `-1` (`AUDIO_SOURCE_INVALID`),
because anything else takes the *capture* branch of the policy and answers with microphones, and the
argument is preceded by a `1`, the non-null marker `Parcel.writeTypedObject` writes — without it the
service reads a null object and answers with an exception. The reply is a list of
`AudioDeviceAttributes`, walked only as far as the first sink; a device carrying audio profiles ends
the walk, since reaching the next entry would mean decoding one.

The volume is the three getters on `STREAM_MUSIC` plus `isStreamMute`, turned into a percentage against
the reported minimum and maximum; `isMusicActive()` adds the `active` bit, and a Bluetooth sink is
additionally named by `getBtActiveDeviceName()`. `platformApi` is the constant `AudioService`, and
`identifier` is the AOSP constant name of the device type without its prefix (`BUILTIN_SPEAKER`),
because the reply's own address is blanked by the service for an unprivileged caller.
