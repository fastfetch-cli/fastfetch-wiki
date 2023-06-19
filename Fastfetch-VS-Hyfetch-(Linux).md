Following benchmarks were performed using [hyperfine](https://github.com/sharkdp/hyperfine) with neowofetch 7.3.9 (installed with hyfetch [1.4.9](https://github.com/hykilpikonna/hyfetch/releases/tag/1.4.9) and fastfetch [0540930](https://github.com/fastfetch-cli/fastfetch/commit/054093025e9b9773853151665ee1cd635f0e7953), running on Fedora Linux 38. All supported modules except Public IP (because it uses Network), GPU Driver (because not supported by fastfetch) in neowofetch were enabled, and fastfetch was set up with the equivalent modules enabled.

```
$ env NO_CONFIG=1 hyperfine -w 3 neowofetch './fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:command:display:de:wm:theme:icons:cursor:terminal:terminalfont:cpu:gpu:memory:wifi:bios:disk:battery:font:media:player:localip:users:locale:break:colors --command-key Editor --command-text "$EDITOR -v | head -1"'
Benchmark 1: neowofetch
  Time (mean ± σ):     960.6 ms ±  87.2 ms    [User: 297.4 ms, System: 325.2 ms]
  Range (min … max):   847.3 ms … 1047.8 ms    10 runs
 
Benchmark 2: ./fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:command:display:de:wm:theme:icons:cursor:terminal:terminalfont:cpu:gpu:memory:wifi:bios:disk:battery:font:media:player:localip:users:locale:break:colors --command-key Editor --command-text "$EDITOR -v | head -1"
  Time (mean ± σ):     113.5 ms ±  36.3 ms    [User: 73.4 ms, System: 48.4 ms]
  Range (min … max):    96.8 ms … 217.7 ms    29 runs
 
  Warning: Statistical outliers were detected. Consider re-running this benchmark on a quiet system without any interferences from other programs. It might help to use the '--warmup' or '--prepare' options.
 
Summary
  ./fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:command:display:de:wm:theme:icons:cursor:terminal:terminalfont:cpu:gpu:memory:wifi:bios:disk:battery:font:media:player:localip:users:locale:break:colors --command-key Editor --command-text "$EDITOR -v | head -1" ran
    8.46 ± 2.81 times faster than neowofetch
```

Output of neowofetch

```
             .',;::::;,'.                carter@fedora 
         .';:cccccccccccc:;,.            ------------- 
      .;cccccccccccccccccccccc;.         OS: Fedora Linux 38 (Workstation Edition) x86_64 
    .:cccccccccccccccccccccccccc:.       Host: Raider GE76 12UGS REV:1.0 
  .;ccccccccccccc;.:dddl:.;ccccccc;.     Kernel: 6.3.8-200.fc38.x86_64 
 .:ccccccccccccc;OWMKOOXMWd;ccccccc:.    Uptime: 1 hour, 27 mins 
.:ccccccccccccc;KMMc;cc;xMMc;ccccccc:.   Packages: 2517 (dnf), 10 (brew) 
,cccccccccccccc;MMM.;cc;;WW:;cccccccc,   Shell: fish 3.6.1 
:cccccccccccccc;MMM.;cccccccccccccccc:   Editor: nvim NVIM v0.9.1 
:ccccccc;oxOOOo;MMM0OOk.;cccccccccccc:   Resolution: 2560x1440 
cccccc;0MMKxdd:;MMMkddc.;cccccccccccc;   DE: Plasma 5.27.5 [KF5 5.107.0] [Qt 5.15.10] (wayland) 
ccccc;XM0';cccc;MMM.;cccccccccccccccc'   WM: kwin_wayland_wr kwin_wayland 
ccccc;MMo;ccccc;MMW.;ccccccccccccccc;    Theme: Breeze [GTK2/3] 
ccccc;0MNc.ccc.xMMd;ccccccccccccccc;     Icons: breeze-dark [GTK2/3] 
cccccc;dNMWXXXWM0:;cccccccccccccc:,      Cursor: breeze_cursors [GTK2/3] 
cccccccc;.:odl:.;cccccccccccccc:,.       Terminal: konsole 
:cccccccccccccccccccccccccccc:'.         Terminal Font: Source Code Pro Light 10 
.:cccccccccccccccccccccc:;,..            CPU: 12th Gen Intel i7-12700H (20) @ 4.6GHz 
  '::cccccccccccccc::;,.                 GPU: NVIDIA Geforce RTX 3070 Ti Laptop GPU 
                                         GPU: Intel Alder Lake-P 
                                         Memory: 4.41 GiB / 31.03 GiB (14%) 
                                         Network: Wifi6 
                                         BIOS: American Megatrends International, LLC. 2.11 (03/27/2023) 
                                         Disk (/): 9.5G / 19G (54%) 
                                         Battery1: 53% [Not charging] 
                                         Font: Noto Sans, 10 [GTK2/3] 
                                         Song: Unknown Artist - Unknown Album - Unknown Song 
                                         Music Player: plasma-browser-integration 
                                         Local IP: 192.168.1.8 
                                         Users: carter 
                                         Locale: zh_CN.UTF-8
```

Note: the detected song and player are wrong

Output of fastfetch

```
             .',;::::;,'.                 carter@fedora
         .';:cccccccccccc:;,.             -------------
      .;cccccccccccccccccccccc;.          OS: Fedora Linux 38 (Workstation Edition) x86_64
    .:cccccccccccccccccccccccccc:.        Host: Raider GE76 12UGS (REV:1.0)
  .;ccccccccccccc;.:dddl:.;ccccccc;.      Kernel: 6.3.8-200.fc38.x86_64
 .:ccccccccccccc;OWMKOOXMWd;ccccccc:.     Uptime: 1 hour, 30 mins
.:ccccccccccccc;KMMc;cc;xMMc;ccccccc:.    Packages: 2522 (rpm), 10 (brew)
,cccccccccccccc;MMM.;cc;;WW:;cccccccc,    Shell: fish 3.6.1
:cccccccccccccc;MMM.;cccccccccccccccc:    Editor: NVIM v0.9.1
:ccccccc;oxOOOo;MMM000k.;cccccccccccc:    Display: 2560x1440 @ 240Hz (as 1280x720)
cccccc;0MMKxdd:;MMMkddc.;cccccccccccc;    DE: KDE Plasma 5.27.5
ccccc;XMO';cccc;MMM.;cccccccccccccccc'    WM: KWin (Wayland)
ccccc;MMo;ccccc;MMW.;ccccccccccccccc;     Theme: Breeze (Dark) [QT], Breeze [GTK3]
ccccc;0MNc.ccc.xMMd;ccccccccccccccc;      Icons: breeze-dark [QT], breeze-dark [GTK3/4]
cccccc;dNMWXXXWM0:;cccccccccccccc:,       Cursor: breeze (24px)
cccccccc;.:odl:.;cccccccccccccc:,.        Terminal: konsole 23.4.2
ccccccccccccccccccccccccccccc:'.          Terminal Font: Source Code Pro Light (10pt, Regular)
:ccccccccccccccccccccccc:;,..             CPU: 12th Gen Intel(R) Core(TM) i7-12700H (20) @ 4.6 GHz
 ':cccccccccccccccc::;,.                  GPU 1: Intel Alder Lake-P Integrated Graphics Controller
                                          GPU 2: NVIDIA Geforce RTX 3070 Ti Laptop GPU
                                          Memory: 4.02 GiB / 31.04 GiB (12%)
                                          Wifi: CMCC-bUNZ-5G - WPA WPA2
                                          Bios: 2.11 (E17K4IMS.20B)
                                          Disk (/): 10.19 GiB / 18.56 GiB (54%) - btrfs
                                          Battery: 53% [Not charging]
                                          Font: Noto Sans (10pt) [QT], Noto Sans (10pt) [GTK3/4]
                                          Media: 周杰伦 - 发如雪
                                          Media Player: lx-music-desktop
                                          Local IP (wlo1): 192.168.1.8
                                          Users: carter
                                          Locale: zh_CN.UTF-8
```