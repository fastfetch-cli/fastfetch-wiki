Following benchmarks were performed using [hyperfine](https://github.com/sharkdp/hyperfine) with neowofetch 7.3.9 (installed with hyfetch [1.4.9](https://github.com/hykilpikonna/hyfetch/releases/tag/1.4.9)) and fastfetch [f705978](https://github.com/fastfetch-cli/fastfetch/commit/f7059783dc7c38d08c7b7435c689c4754909b95e), running on GhostBSD 23.06.01 (Live CD).

All supported modules except Public IP (because it uses Network) neowofetch were enabled, and fastfetch was set up with the equivalent modules enabled.

```
hyperfine -w 3 neowofetch './fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:command:display:de:wm:wmtheme:theme:icons:cursor:terminal:terminalfont:cpu:gpu:memory:disk:battery:font:media:player:localip:users:locale:break:colors --command-text "$EDITOR -v | head -1" --command-key Editor'
Benchmark 1: neowofetch
  Time (mean ± σ):     767.4 ms ±  64.3 ms    [User: 271.5 ms, System: 436.3 ms]
  Range (min … max):   686.4 ms … 892.9 ms    10 runs
 
Benchmark 2: ./fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:command:display:de:wm:wmtheme:theme:icons:cursor:terminal:terminalfont:cpu:gpu:memory:disk:battery:font:media:player:localip:users:locale:break:colors --command-text "$EDITOR -v | head -1" --command-key Editor
  Time (mean ± σ):      82.6 ms ±  31.3 ms    [User: 28.7 ms, System: 23.3 ms]
  Range (min … max):    52.1 ms … 207.1 ms    38 runs
 
Summary
  './fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:command:display:de:wm:wmtheme:theme:icons:cursor:terminal:terminalfont:cpu:gpu:memory:disk:battery:font:media:player:localip:users:locale:break:colors --command-text "$EDITOR -v | head -1" --command-key Editor' ran
    9.29 ± 3.60 times faster than 'neowofetch'
```

Output of neowofetch

```
           ,gggggg.    ghostbsd@livecd 
        ,agg9*   .g)   --------------- 
      .agg* ._.,gg*    OS: GhostBSD 23.06.01 amd64 
    ,gga*  (ggg*'      Host: REV:1.0 
   ,ga*       ,ga*     Kernel: 13.2-STABLE 
  ,ga'     .ag*        Uptime: 3 hours, 26 mins 
 ,ga'   .agga'         Packages: 798 (pkg) 
 9g' .agg'g*,a         Shell: fish 3.6.1 
 'gggg*',gga'          Editor: vi vi: illegal option -- - 
      .gg*'            Resolution: 2560x1440 @ 0.00Hz 
    .gga*              DE: GNOME 42.4 (x11) 
  .gga*                WM: Mutter 
 (ga*                  WM Theme: Adwaita 
                       Theme: Adwaita [GTK2/3] 
                       Icons: Adwaita [GTK2/3] 
                       Cursor: Adwaita [GTK2/3] 
                       Terminal: gnome-terminal 
                       CPU: 12th Gen Intel i7-12700H (20) @ 2.6GHz 
                       GPU: GA104 [Geforce RTX 3070 Ti Laptop GPU] 
                       Memory: 4.78 GiB / 31.63 GiB (15%) 
                       Disk (/): 3.4G / 23G (15%) 
                       Battery: 61% Charging 
                       Font: Cantarell 11 [GTK2/3] 
                       Local IP: 192.168.31.86 
                       Users: ghostbsd 
                       Locale: en_US.UTF-8
```

Output of fastfetch

```
           ,gggggg.     ghostbsd@livecd
        ,agg9*   .g)    ---------------
      .agg* ._.,gg*     OS: GhostBSD 23.06.01 amd64
    ,gga*  (ggg*'       Host: Raider GE76 12UGS (REV:1.0)
   ,ga*       ,ga*      Kernel: 13.2-STABLE
  ,ga'     .ag*         Uptime: 3 hours, 27 mins
 ,ga'   .agga'          Packages: 798 (pkg)
 9g' .agg'g*,a          Shell: fish 3.6.1
 'gggg*',gga'           Display (default): 2560x1440
      .gg*'             DE: Gnome 42.4
    .gga*               WM: Mutter (X11)
  .gga*                 WM Theme: Adwaita
 (ga*                   Theme: Adwaita [GTK2/3/4]
                        Icons: Adwaita [GTK2/3/4]
                        Cursor: Adwaita (24px)
                        Terminal: gnome-terminal 3.44.2
                        Terminal Font: Droid Sans Mono (12pt)
                        CPU: 12th Gen Intel(R) Core(TM) i7-12700H (20) @ 2.688 GHz
                        GPU 1: NVIDIA Geforce RTX 3070 Ti Laptop GPU
                        GPU 2: Intel Alder Lake-P Integrated Graphics Controller
                        Memory: 4.81 GiB / 31.64 GiB (15%)
                        Disk (/): 3.40 GiB / 22.53 GiB (15%) - zfs
                        Disk (/mnt/windows): 833 GiB / 934 GiB (89%) - fusefs [External]
                        Battery: 61% [AC connected]
                        Font: Cantarell (11pt) [GTK2/3/4]
                        Media: 原神-Genshin-公式 - 【原神】キャラクター実戦紹介　魈(CV：松岡禎丞)「劫を経りて、悪祟を祓う」
                        Media Player: Chromium
                        Local IP (wlan0): 192.168.31.86
                        Users: ghostbsd
                        Locale: en_US.UTF-8
```

Notes:

1. `vi` doesn't support `--version`. Neowofetch uses the error message as version string, while fastfetch ignores it.
1. My DE was running in software rendering, therefore no refresh rates.
1. Neowofetch doesn't support mountpoint auto-detection, therefore my Windows partition wasn't found.
1. My battery wasn't charging. It was set to 60% as the maximum capacity.
1. Neowofetch didn't detect song and player for some reason.