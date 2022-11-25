Following benchmarks were performed using [hyperfine](https://github.com/sharkdp/hyperfine) with hyfetch [1.4.4](https://github.com/hykilpikonna/hyfetch/releases/tag/1.4.4) and fastfetch [3c7f02b](https://github.com/LinusDierheimer/fastfetch/commit/3c7f02bee34afa65fb87de9ad46d542af79e3da9), running on FreeBSD (13.1). All supported modules except Public IP (because it uses Network) in hyfetch were enabled, and fastfetch was set up with the equivalent modules enabled.

```
hyperfine -w 3 "hyfetch" "./fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:resolution:de:wm:wmtheme:icons:cursor:terminal:terminalfont:cpu:gpu:memory:disk:font:media:player:localip:users:locale --nocache"
Benchmark 1: hyfetch
  Time (mean ± σ):     267.3 ms ±   2.2 ms    [User: 154.1 ms, System: 109.5 ms]
  Range (min … max):   264.4 ms … 271.8 ms    11 runs
 
Benchmark 2: ./fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:resolution:de:wm:wmtheme:icons:cursor:terminal:terminalfont:cpu:gpu:memory:disk:font:media:player:localip:users:locale --nocache
  Time (mean ± σ):       9.8 ms ±   0.4 ms    [User: 6.4 ms, System: 4.2 ms]
  Range (min … max):     9.1 ms …  11.6 ms    281 runs
 
Summary
  './fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:resolution:de:wm:wmtheme:icons:cursor:terminal:terminalfont:cpu:gpu:memory:disk:font:media:player:localip:users:locale --nocache' ran
   27.30 ± 1.12 times faster than 'hyfetch'
```

Output of hyfetch

```
```                        `       root@FreeBSD 
  ` `.....---.......--.```   -/    ------------ 
  +o   .--`         /y:`      +.   OS: FreeBSD 13.1-RELEASE aarch64 
   yo`:.            :o      `+-    Uptime: 19 mins 
    y/               -/`   -o/     Packages: 415 (pkg) 
   .-                  ::/sy+:.    Shell: fish 3.5.1 
   /                     `--  /    Resolution: 1024x768 @ 0.00Hz 
  `:                          :`   DE: Xfce 4.16 () 
  `:                          :`   WM: Xfwm4 
   /                          /    WM Theme: Default 
   .-                        -.    Theme: Adwaita [GTK3] 
    --                      -.     Icons: Adwaita [GTK2/3] 
     `:`                  `:`      Cursor: Adwaita [GTK3] 
       .--             `--.        Terminal: xfce4-terminal 
          .---.....----.           Terminal Font: DejaVu Sans Mono 12 
                                   CPU: ARM Unknown r0p0 (midr: 410f0000) (4) 
                                   Memory: 1.42 GiB / 2.99 GiB (47%) 
                                   Disk (/): 3.3G / 120G (3%) 
                                   Font: WenQuanYi Zen Hei Medium 10 [GTK2/3] 
                                   Local IP: 10.211.55.8 
                                   Users: root 
                                   Locale: C.UTF-8
```

Output of fastfetch

```
```                        `        root@FreeBSD
  ` `.....---.......--.```   -/     ------------
  +o   .--`         /y:`      +.    OS: FreeBSD 13.1 arm64
   yo`:.            :o      `+-     Host: Parallels ARM Virtual Machine
    y/               -/`   -o/      Kernel: 13.1-RELEASE
   .-                  ::/sy+:.     Uptime: 19 mins
   /                     `--  /     Packages: 415 (pkg)
  `:                          :`    Shell: fish 3.5.1
  `:                          :`    Resolution: 1024x768
   /                          /     DE: Xfce4
   .-                        -.     WM: Xfwm4 (X11)
    --                      -.      WM Theme: Default
     `:`                  `:`       Icons: Adwaita [GTK2/3/4]
       .--             `--.         Terminal: xfce4-terminal
          .---.....----.            Terminal Font: DejaVu Sans Mono (12pt)
                                    CPU: ARM Unknown r0p0 (midr: 410f0000) (4)
                                    Memory: 1.31 GiB / 2.90 GiB (45%)
                                    Disk (/): 3.27 GiB / 120 GiB (2%)
                                    Disk (/usr/home): 96.00 KiB / 117 GiB (0%)
                                    Disk (/usr/ports): 756.83 MiB / 118 GiB (0%) [Removable]
                                    Disk (/usr/src): 96.00 KiB / 117 GiB (0%)
                                    Font: WenQuanYi Zen Hei Medium (10pt) [GTK2/3/4]
                                    Local IP (vtnet0): 10.211.55.8
                                    Users: root
                                    Locale: C.UTF-8
```