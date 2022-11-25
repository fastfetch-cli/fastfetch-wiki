Following benchmarks were performed using [hyperfine](https://github.com/sharkdp/hyperfine) with hyfetch [1.4.4](https://github.com/hykilpikonna/hyfetch/releases/tag/1.4.4) and fastfetch [3c7f02b](https://github.com/LinusDierheimer/fastfetch/commit/3c7f02bee34afa65fb87de9ad46d542af79e3da9), running on macOS Ventura (13.0.1). All supported modules except `Public IP` (because it uses Network) and `GPU Driver` (because not supported by fastfetch) in hyfetch were enabled, and fastfetch was set up with the equivalent modules enabled.

```
$ hyperfine -w 3 "hyfetch" "fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:resolution:de:wm:wmtheme:terminal:terminalfont:cpu:gpu:memory:disk:battery:poweradapter:song:player:localip:users:locale --nocache"
Benchmark 1: hyfetch
  Time (mean ± σ):      1.160 s ±  0.032 s    [User: 0.352 s, System: 0.371 s]
  Range (min … max):    1.125 s …  1.200 s    10 runs

Benchmark 2: fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:resolution:de:wm:wmtheme:terminal:terminalfont:cpu:gpu:memory:disk:battery:poweradapter:song:player:localip:users:locale --nocache
  Time (mean ± σ):      72.3 ms ±   3.6 ms    [User: 16.3 ms, System: 16.8 ms]
  Range (min … max):    66.7 ms …  80.3 ms    43 runs

Summary
  'fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:resolution:de:wm:wmtheme:terminal:terminalfont:cpu:gpu:memory:disk:battery:poweradapter:song:player:localip:users:locale --nocache' ran
   16.04 ± 0.92 times faster than 'hyfetch'
```

Output of `hyfetch`

```
                    c.'          carter@litongzhoudeMacBook-Pro
                 ,xNMM.          ------------------------------
               .OMMMMo           OS: macOS Ventura 13.0.1 (22A400) arm64
               lMM"              Host: MacBook Pro (16-inch, 2021)
     .;loddo:.  .olloddol;.      Kernel: 22.1.0
   cKMMMMMMMMMMNWMMMMMMMMMM0:    Uptime: 3 hours, 46 mins
 .KMMMMMMMMMMMMMMMMMMMMMMMWd.    Packages: 262 (brew)
 XMMMMMMMMMMMMMMMMMMMMMMMX.      Shell: fish 3.5.1
;MMMMMMMMMMMMMMMMMMMMMMMM:       Resolution: 3456x2234
:MMMMMMMMMMMMMMMMMMMMMMMM:       DE: Aqua ()
.MMMMMMMMMMMMMMMMMMMMMMMMX.      WM: Quartz Compositor
 kMMMMMMMMMMMMMMMMMMMMMMMMWd.    WM Theme: Blue (Dark)
 'XMMMMMMMMMMMMMMMMMMMMMMMMMMk   Terminal: iTerm2
  'XMMMMMMMMMMMMMMMMMMMMMMMMK.   Terminal Font: MapleMonoSCNF-Regular 16
    kMMMMMMMMMMMMMMMMMMMMMMd     CPU: Apple M1 Pro (10)
     ;KMMMMMMMWXXWMMMMMMMk.      GPU: Apple M1 Pro (16)
       "cooc*"    "*coo'"        Memory: 11.38 GiB / 16.00 GiB (71%)
                                 Disk (/): 459G / 927G (50%)
                                 Battery: 80%
                                 Power Adapter: 140W
                                 Song: Unknown Artist - Unknown Album - Unknown Song
                                 Music Player: Music
                                 Local IP: 172.16.134.31
                                 Users: carter
                                 Locale: zh_CN.UTF-8
```

Output of fastfetch

```
                     c.'          carter@litongzhoudeMacBook-Pro.local
                 ,xNMM.           ------------------------------------
               .OMMMMo            OS: macOS Ventura 13.0.1 22A400 arm64
               lMM"               Host: MacBook Pro (16-inch, 2021)
     .;loddo:.  .olloddol;.       Kernel: 22.1.0
   cKMMMMMMMMMMNWMMMMMMMMMM0:     Uptime: 3 hours, 48 mins
 .KMMMMMMMMMMMMMMMMMMMMMMMWd.     Packages: 262 (brew)
 XMMMMMMMMMMMMMMMMMMMMMMMX.       Shell: fish 3.5.1
;MMMMMMMMMMMMMMMMMMMMMMMM:        Resolution: 1728x1117 @ 120Hz
:MMMMMMMMMMMMMMMMMMMMMMMM:        DE: Aqua
.MMMMMMMMMMMMMMMMMMMMMMMMX.       WM: Quartz Compositor
 kMMMMMMMMMMMMMMMMMMMMMMMMWd.     WM Theme: Multicolor (Dark)
 'XMMMMMMMMMMMMMMMMMMMMMMMMMMk    Terminal: iTerm
  'XMMMMMMMMMMMMMMMMMMMMMMMMK.    Terminal Font: MapleMonoSCNF-Regular (16pt)
    kMMMMMMMMMMMMMMMMMMMMMMd      CPU: Apple M1 Pro (10)
     ;KMMMMMMMWXXWMMMMMMMk.       GPU: Apple M1 Pro (16)
       "cooc*"    "*coo'"         Memory: 7.46 GiB / 16.00 GiB (46%)
                                  Disk (/): 459 GiB / 926 GiB (49%)
                                  Disk (/Volumes/EOITEK): 50.31 MiB / 14.83 GiB (0%) [Removable]
                                  Battery: 80%
                                  Power Adapter: 140W USB-C Power Adapter
                                  Media: HOYO-MiX - Village Surrounded by Green 葳蕤林野间 (Playing)
                                  Media Player: NeteaseMusic
                                  Local IP (en0): 172.16.134.31
                                  Local IP (bridge100): 10.211.55.2
                                  Local IP (bridge101): 10.37.129.2
                                  Users: carter
                                  Locale: zh_CN.UTF-8
```