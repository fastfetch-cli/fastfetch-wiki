Following benchmarks were performed using [hyperfine](https://github.com/sharkdp/hyperfine) with hyfetch [1.4.4](https://github.com/hykilpikonna/hyfetch/releases/tag/1.4.4) and fastfetch [3c7f02b](https://github.com/LinusDierheimer/fastfetch/commit/3c7f02bee34afa65fb87de9ad46d542af79e3da9), running on Ubuntu Lunar (23.04). All supported modules except Public IP (because it uses Network), GPU Driver and Network (because not supported by fastfetch) in hyfetch were enabled, and fastfetch was set up with the equivalent modules enabled.

```
$ hyperfine -w 3 "hyfetch" "./fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:resolution:de:wm:wmtheme:icons:cursor:terminal:terminalfont:cpu:gpu:memory:disk:font:media:player:localip:users:locale --nocache"
Benchmark 1: hyfetch
  Time (mean ± σ):     461.3 ms ±   4.9 ms    [User: 301.8 ms, System: 143.7 ms]
  Range (min … max):   453.6 ms … 468.8 ms    10 runs
 
Benchmark 2: ./fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:resolution:de:wm:wmtheme:icons:cursor:terminal:terminalfont:cpu:gpu:memory:disk:font:media:player:localip:users:locale --nocache
  Time (mean ± σ):      13.8 ms ±   1.2 ms    [User: 8.6 ms, System: 5.7 ms]
  Range (min … max):    12.5 ms …  19.5 ms    193 runs
 
Summary
  './fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:resolution:de:wm:wmtheme:icons:cursor:terminal:terminalfont:cpu:gpu:memory:disk:font:media:player:localip:users:locale --nocache' ran
   33.48 ± 2.92 times faster than 'hyfetch'
```

Output of hyfetch

```
                             ....             carter@Ubuntu-PC 
              .',:clooo:  .:looooo:.          ---------------- 
           .;looooooooc  .oooooooooo'         OS: Ubuntu Lunar Lobster (development branch) aarch64 
        .;looooool:,''.  :ooooooooooc         Host: Parallels ARM Virtual Machine Parallels ARM Virtual Platform 
       ;looool;.         'oooooooooo,         Kernel: 5.19.0-21-generic 
      ;clool'             .cooooooc.  ,,      Uptime: 1 hour, 48 mins 
         ...                ......  .:oo,     Packages: 1598 (dpkg), 11 (snap) 
  .;clol:,.                        .loooo'    Shell: fish 3.5.1 
 :ooooooooo,                        'ooool    Resolution: 3456x2160 
'ooooooooooo.                        loooo.   DE: GNOME 43.0 (wayland) 
'ooooooooool                         coooo.   Theme: Yaru-dark [GTK2/3] 
 ,loooooooc.                        .loooo.   Icons: Yaru-dark [GTK2/3] 
   .,;;;'.                          ;ooooc    Cursor: Yaru [GTK2/3] 
       ...                         ,ooool.    Terminal: gnome-terminal-server 
    .cooooc.              ..',,'.  .cooo.     CPU: ARM - (4) 
      ;ooooo:.           ;oooooooc.  :l.      GPU: Red Hat, Inc. Virtio GPU 
       .coooooc,..      coooooooooo.          Memory: 2.81 GiB / 3.84 GiB (73%) 
         .:ooooooolc:. .ooooooooooo'          Disk (/): 12G / 64G (18%) 
           .':loooooo;  ,oooooooooc           Font: Ubuntu 11 [GTK2/3] 
               ..';::c'  .;loooo:'            Song: Unknown Artist - Unknown Album - Unknown Song 
                             .                Music Player: gnome-music 
                                              Local IP: 10.211.55.10 
                                              Users: carter 
                                              Locale: zh_CN.UTF-8                 
```

Output of fastfetch

```
                            ....               carter@Ubuntu-PC
              .',:clooo:  .:looooo:.           ----------------
           .;looooooooc  .oooooooooo'          OS: Ubuntu 23.04 aarch64
        .;looooool:,''.  :ooooooooooc          Host: Parallels ARM Virtual Machine (0.1)
       ;looool;.         'oooooooooo,          Kernel: 5.19.0-21-generic
      ;clool'             .cooooooc.  ,,       Uptime: 1 hour, 47 mins
         ...                ......  .:oo,      Packages: 1598 (dpkg), 11 (snap)
  .;clol:,.                        .loooo'     Shell: fish 3.5.1
 :ooooooooo,                        'ooool     Resolution: 3456x2160 @ 60Hz
'ooooooooooo.                        loooo.    DE: GNOME 43.0
'ooooooooool                         coooo.    WM: Mutter (Wayland)
 ,loooooooc.                        .loooo.    WM Theme: Yaru-dark
   .,;;;'.                          ;ooooc     Icons: Yaru-dark [GTK2/3/4]
       ...                         ,ooool.     Cursor: Yaru (24px)
    .cooooc.              ..',,'.  .cooo.      Terminal: gnome-terminal-server
      ;ooooo:.           ;oooooooc.  :l.       Terminal Font: Iosevka (12pt)
       .coooooc,..      coooooooooo.           CPU: CPU (4)
         .:ooooooolc:. .ooooooooooo'           GPU: Red Hat, Inc. Virtio GPU
           .':loooooo;  ,oooooooooc            Memory: 2.61 GiB / 3.82 GiB (68%)
               ..';::c'  .;loooo:'             Disk (/): 12.69 GiB / 63.52 GiB (19%)
                             .                 Disk (/home): 12.69 GiB / 63.52 GiB (19%)
                                               Disk (/var/snap/firefox/common/host-hunspell): 12.69 GiB / 63.52 GiB (19%)
                                               Font: Ubuntu (11pt) [GTK2/3/4]
                                               Media: 未知艺术家 - 周深 - Rubia
                                               Media Player: Music
                                               Local IP (enp0s5): 10.211.55.10
                                               Users: carter
                                               Locale: zh_CN.UTF-8
```