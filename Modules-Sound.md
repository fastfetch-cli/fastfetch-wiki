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
| Linux | `sound_linux.c` | PulseAudio's own native protocol over the daemon socket; when no server answers, ALSA over the kernel's control interface |
| Android | `sound_android.c` + `sound_linux.c` | The `audio` (IAudioService) system service over raw binder while SurfaceFlinger is the display server, otherwise the Linux code. Needs no permission (see [Android Permissions](Android-Permissions)) |
| FreeBSD / MidnightBSD / DragonFly | `sound_bsd.c` | `ioctl(SNDCTL_MIXER_*)` on `/dev/mixer*` |
| NetBSD | `sound_nbsd.c` | `ioctl(AUDIO_GETDEV)` … on `/dev/audio*` |
| OpenBSD | `sound_obsd.c` | `ioctl(AUDIO_GETDEV)` … on `/dev/audio*` |
| Solaris / illumos | `sound_sunos.c` | OSS `ioctl(SNDCTL_SYSINFO)` |
| macOS | `sound_apple.c` | CoreAudio |
| Windows | `sound_windows.cpp` | Core Audio APIs (`IMMDeviceEnumerator`) |
| Haiku | `sound_haiku.cpp` | `BMediaRoster` / the media kit |
| GNU/Hurd | `sound_linux.c` | Same code. Hurd has neither the Linux sound headers nor `/proc/asound`, so only the sound server path can answer there |

Every platform has an implementation. Which device ends up in the list is decided by `soundType`,
and each backend applies that filter in its own way — see the pitfalls.

Android compiles both files and the display server decides between them, the same arrangement as
`wallpaper` and `media`. SurfaceFlinger means the sound that is audible is Android's own, so
`sound_android.c` asks `IAudioService` over `/dev/binder`; a Termux:X11 or Wayland session runs a real
desktop on top of the device, and what plays there belongs to that desktop, so `sound_linux.c` —
compiled alongside for exactly that reason — answers, with the same two backends it has on a desktop.
The ALSA one is not compiled on Android at all, since `/dev/snd` and `/proc/asound` are closed to an
app. Only one of the two files is ever consulted, never merged.

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
  (`null` in the JSON, and no `(…%)` suffix). On Linux a muted sink, and a muted ALSA control,
  report **`0%`**. Neither is "unknown" — they are genuinely different values for the same user
  action.
- **Linux has two backends, and only one of them answers per run.** PulseAudio is asked first, because
  it is the only one that knows the volume of the desktop rather than of the hardware, and ALSA is
  consulted only when that path returns an error. That is what a machine running plain ALSA does, and
  one where PipeWire runs with no socket at the usual path; a machine with neither reports the sound
  server's error, as before.
- **The ALSA path reports one device per sound card.** `identifier` is `hw:N`, the card index that
  ALSA's own tools also take after `-c`, and `name` is the name of that card's main playback control
  with its direction suffix stripped — `Master`, not `Master Playback Volume` — which is what a
  desktop mixer puts on the slider. The control is picked from `Master`, `Master Mono`, `PCM`,
  `Speaker`, `Headphone`, `Headphones` and `Front` in that order, and the first playback volume
  control otherwise: falling back to the first control of the card would follow the order the driver
  registered things in, which is not a ranking. A card with a single unnamed volume, as a codec that
  registers `Playback Volume`, is named after the card itself.
- **The ALSA volume is the control's own scale**, converted to a percentage linearly, which is the
  number `amixer` prints in brackets. A control whose range is empty or inverted reports no volume.
- **A capture control is never a device there.** `Capture Volume` is an input gain, so a card that
  carries nothing else — a USB microphone, for example — has no output to report.
- **`volume: null` on the ALSA path has two meanings.** The card's control device could not be opened
  (the nodes are `root:audio` behind an ACL for the seat, so a session over ssh can name a card it may
  not read — that card is still reported, by its own name), or it has no control whose value could be
  read.
- **`main` and `active` on the ALSA path.** `main` is the first card that has an output at all, and
  `active` is the playback switch of the control being reported. A control without a switch, and a
  card whose controls could not be read, count as active rather than as unknown.
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
- **`platformApi` is not a constant across platforms.** Windows always reports `Core Audio APIs` and
  Android always reports `AudioService`; Linux reports the PulseAudio server name and version, or the
  constant `ALSA` when the fallback answered, so a script cannot treat the field as an enum.
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
  `condition.succeeded`. This was fixed; it is the only module that ever had the defect.

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

Neither backend is a library client, so no `libpulse` and no `libasound` is linked or loaded at
runtime, and there is no build option for either.

`sound_linux.c` asks the sound server first. It is reached at `$PULSE_SERVER`, or at the socket
under `$PULSE_RUNTIME_PATH`, and the two queries a library client would make are sent as PulseAudio's
own native protocol commands: the server information first (the server name, with a `(on …)` wrapper
stripped, and the default sink name), then the sink list. A sink is `main` when its name equals the
default sink name and `active` when it has an active port whose availability is not
`PA_PORT_AVAILABLE_NO`. In `main` mode the enumeration stops as soon as one device has been
collected, so only the default sink is reported. `platformApi` is the PulseAudio server string, not a
constant, and `identifier` is the sink name. The volume is
`volume.values[0] * 100 / PA_VOLUME_NORM` rounded — and it is forced to **0** when the sink is muted,
which is the opposite of what the Windows backend does.

When that returns an error, the ALSA path runs. It needs no library either: the cards are read from
`/proc/asound/cards`, which is world readable, and each card's controls over `/dev/snd/controlC<N>`
with the kernel's own control interface — `SNDRV_CTL_IOCTL_ELEM_LIST` to walk the control ids,
`ELEM_INFO` for the type and the value range, `ELEM_READ` for the value. The structs and the request
numbers come from `<sound/asound.h>`, the kernel's uapi header, so nothing about the abi is written
down here; a request number carries the size of the struct it passes, which is why a header that does
not match the kernel is refused as an unknown ioctl instead of being read as garbage. A libc without
that header simply compiles the fallback out, and Android leaves it out too, where those two paths
are closed to an app anyway.

What a control *is* is not in that interface: an access bitmask with no volume and no switch bit, and
a type. It is read from the control's name instead, which is a base name plus a suffix — `Master
Playback Volume`, or `Capture Volume` for a control with no base name of its own — and a suffix only
counts when it is the whole name or starts a word of it. The suffixes are the ones libasound's simple
mixer layer carries, tried longest first. Capture and routing controls are not outputs and are never
reported, which is a deliberate difference from libasound, where `Capture Volume` is a mixer slider
like any other.

The switch that mutes a control is a second control with the same base name and index, so it takes a
second walk of the card — which base name wins is only known once the first walk is over. A control
without a switch leaves the device active.

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
