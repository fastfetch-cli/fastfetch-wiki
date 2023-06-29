Following benchmarks were performed using [hyperfine](https://github.com/sharkdp/hyperfine) with neowofetch 7.3.9 (installed with hyfetch [1.4.9](https://github.com/hykilpikonna/hyfetch/releases/tag/1.4.9)) and fastfetch [9794c4b](https://github.com/fastfetch-cli/fastfetch/commit/9794c4b78f239eeb4a559661a95dc1dc504d578f), running on macOS Ventura (13.3.1). All supported modules except `Public IP` (because it uses Network) and `GPU Driver` (because not supported by fastfetch) in hyfetch were enabled, and fastfetch was set up with the equivalent modules enabled.

```
$ env NO_CONFIG=1 hyperfine -w 3 neowofetch './fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:command:display:de:wm:wmtheme:terminal:terminalfont:cpu:gpu:memory:wifi:disk:battery:poweradapter:media:player:localip:users:locale:break:colors --command-key Editor --command-text "$EDITOR -v | head -1"'
Benchmark 1: neowofetch
  Time (mean ± σ):     746.6 ms ±  10.2 ms    [User: 227.4 ms, System: 289.6 ms]
  Range (min … max):   728.3 ms … 760.0 ms    10 runs

Benchmark 2: ./fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:command:display:de:wm:wmtheme:terminal:terminalfont:cpu:gpu:memory:wifi:disk:battery:poweradapter:media:player:localip:users:locale:break:colors --command-key Editor --command-text "$EDITOR -v | head -1"
  Time (mean ± σ):      99.2 ms ±   3.2 ms    [User: 24.2 ms, System: 30.0 ms]
  Range (min … max):    90.2 ms … 103.8 ms    28 runs

Summary
  ./fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:command:display:de:wm:wmtheme:terminal:terminalfont:cpu:gpu:memory:wifi:disk:battery:poweradapter:media:player:localip:users:locale:break:colors --command-key Editor --command-text "$EDITOR -v | head -1" ran
    7.53 ± 0.26 times faster than neowofetch
```

Output of `neowofetch`

```
                    c.'          carter@litongzhoudeMacBook-Pro
                 ,xNMM.          ------------------------------
               .OMMMMo           OS: macOS Ventura 13.3.1 (22E261) arm64
               lMM"              Host: MacBook Pro (16-inch, 2021)
     .;loddo:.  .olloddol;.      Kernel: 22.4.0
   cKMMMMMMMMMMNWMMMMMMMMMM0:    Uptime: 7 hours, 43 mins
 .KMMMMMMMMMMMMMMMMMMMMMMMWd.    Packages: 304 (brew)
 XMMMMMMMMMMMMMMMMMMMMMMMX.      Shell: fish 3.6.1
;MMMMMMMMMMMMMMMMMMMMMMMM:       Editor: nvim NVIM v0.9.1
:MMMMMMMMMMMMMMMMMMMMMMMM:       Resolution: 1728x1117 @ 120Hz
.MMMMMMMMMMMMMMMMMMMMMMMMX.      DE: Aqua ()
 kMMMMMMMMMMMMMMMMMMMMMMMMWd.    WM: Quartz Compositor
 'XMMMMMMMMMMMMMMMMMMMMMMMMMMk   WM Theme: Blue (Dark)
  'XMMMMMMMMMMMMMMMMMMMMMMMMK.   Terminal: iTerm2
    kMMMMMMMMMMMMMMMMMMMMMMd     Terminal Font: Sarasa-Term-SC-Nerd-Regular 16
     ;KMMMMMMMWXXWMMMMMMMk.      CPU: Apple M1 Pro (10)
       "cooc*"    "*coo'"        GPU: Apple M1 Pro (16)
                                 Memory: 12.33 GiB / 16.00 GiB (77%)
                                 Network: en0: Wi-Fi@229Mbps
                                 Disk (/): 641G / 927G (70%)
                                 Battery: 100%
                                 Power Adapter: 140W
                                 Song: Unknown Artist - Unknown Album - Unknown Song
                                 Music Player: iTunes
                                 Local IP: 10.62.99.219
                                 Users: carter
                                 Locale: zh_CN.UTF-8
```

Output of fastfetch

```
                     c.'          carter@litongzhoudeMacBook-Pro.local
                 ,xNMM.           ------------------------------------
               .OMMMMo            OS: macOS Ventura 13.3.1 22E261 arm64
               lMM"               Host: MacBook Pro (16-inch, 2021)
     .;loddo:.  .olloddol;.       Kernel: 22.4.0
   cKMMMMMMMMMMNWMMMMMMMMMM0:     Uptime: 7 hours, 47 mins
 .KMMMMMMMMMMMMMMMMMMMMMMMWd.     Packages: 291 (brew), 13 (brew-cask)
 XMMMMMMMMMMMMMMMMMMMMMMMX.       Shell: fish 3.6.1
;MMMMMMMMMMMMMMMMMMMMMMMM:        Editor: NVIM v0.9.1
:MMMMMMMMMMMMMMMMMMMMMMMM:        Display (Color LCD): 3456x2234 @ 120Hz (as 1728x1117)
.MMMMMMMMMMMMMMMMMMMMMMMMX.       DE: Aqua
 kMMMMMMMMMMMMMMMMMMMMMMMMWd.     WM: Quartz Compositor
 'XMMMMMMMMMMMMMMMMMMMMMMMMMMk    WM Theme: Multicolor (Dark)
  'XMMMMMMMMMMMMMMMMMMMMMMMMK.    Terminal: iTerm 3.5.0beta10
    kMMMMMMMMMMMMMMMMMMMMMMd      Terminal Font: Sarasa-Term-SC-Nerd-Regular (16pt)
     ;KMMMMMMMWXXWMMMMMMMk.       CPU: Apple M1 Pro (10)
       "cooc*"    "*coo'"         GPU: Apple M1 Pro (16) [Integrated]
                                  Memory: 5.27 GiB / 16.00 GiB (32%)
                                  Wifi: Ceair-Staff - 802.11ac (Wi-Fi 5) - WPA2 Enterprise
                                  Disk (/): 640 GiB / 926 GiB (69%) - apfs
                                  Battery: 100% [Fully charged]
                                  Power Adapter: 140W USB-C Power Adapter
                                  Media: 秋日的私语 (Playing)
                                  Media Player: IINA
                                  Local IP (en0): 10.62.99.219
                                  Users: carter
                                  Locale: zh_CN.UTF-8
```

Note:

1. Both neowofetch and fastfetch hard coded DE value for macOS, thus no DE version. However leaving a empty `()` doesn't seem good.
1. WM Theme should be Multicolor ( default value of macOS ), though it behaves like blue.
1. Neowofetch fails to detect the song and media player, it doesn't seem to support media players other than iTunes.