Following benchmarks were performed using [hyperfine](https://github.com/sharkdp/hyperfine) with neowofetch 7.3.9 (installed with hyfetch [1.4.9](https://github.com/hykilpikonna/hyfetch/releases/tag/1.4.9)) and fastfetch [68feaa2](https://github.com/fastfetch-cli/fastfetch/commit/68feaa2800e3aa953186f55db07e4892f4e19e9c), running on Fedora Linux 38.

All supported modules except Public IP (because it uses Network), GPU Driver (because not supported by fastfetch) in neowofetch were enabled, and fastfetch was set up with the equivalent modules enabled.

```
$ env NO_CONFIG=1 hyperfine -w 3 neowofetch './fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:command:display:de:wm:theme:icons:cursor:terminal:terminalfont:cpu:gpu:memory:bluetooth:wifi:bios:disk:battery:font:media:player:localip:users:locale:break:colors --allow-slow-operations --command-key Editor --command-text "$EDITOR -v | head -1"'
Benchmark 1: neowofetch
  Time (mean ± σ):     952.6 ms ±  83.4 ms    [User: 289.5 ms, System: 318.5 ms]
  Range (min … max):   850.0 ms … 1059.9 ms    10 runs
 
Benchmark 2: ./fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:command:display:de:wm:theme:icons:cursor:terminal:terminalfont:cpu:gpu:memory:bluetooth:wifi:bios:disk:battery:font:media:player:localip:users:locale:break:colors --allow-slow-operations --command-key Editor --command-text "$EDITOR -v | head -1"
  Time (mean ± σ):      51.7 ms ±  26.0 ms    [User: 17.6 ms, System: 27.5 ms]
  Range (min … max):    43.4 ms … 167.3 ms    61 runs
 
  Warning: Statistical outliers were detected. Consider re-running this benchmark on a quiet system without any interferences from other programs. It might help to use the '--warmup' or '--prepare' options.
 
Summary
  ./fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:command:display:de:wm:theme:icons:cursor:terminal:terminalfont:cpu:gpu:memory:bluetooth:wifi:bios:disk:battery:font:media:player:localip:users:locale:break:colors --allow-slow-operations --command-key Editor --command-text "$EDITOR -v | head -1" ran
   18.42 ± 9.39 times faster than neowofetch
```

Output of neowofetch

```
             .',;::::;,'.                carter@fedora 
         .';:cccccccccccc:;,.            ------------- 
      .;cccccccccccccccccccccc;.         OS: Fedora Linux 38 (Workstation Edition) x86_64 
    .:cccccccccccccccccccccccccc:.       Host: Raider GE76 12UGS REV:1.0 
  .;ccccccccccccc;.:dddl:.;ccccccc;.     Kernel: 6.3.8-200.fc38.x86_64 
 .:ccccccccccccc;OWMKOOXMWd;ccccccc:.    Uptime: 58 mins 
.:ccccccccccccc;KMMc;cc;xMMc;ccccccc:.   Packages: 2527 (dnf), 10 (brew) 
,cccccccccccccc;MMM.;cc;;WW:;cccccccc,   Shell: fish 3.6.1 
:cccccccccccccc;MMM.;cccccccccccccccc:   Editor: nvim NVIM v0.9.1 
:ccccccc;oxOOOo;MMM0OOk.;cccccccccccc:   Resolution: 2560x1440 
cccccc;0MMKxdd:;MMMkddc.;cccccccccccc;   DE: Plasma 5.27.5 [KF5 5.107.0] [Qt 5.15.10] (wayland) 
ccccc;XM0';cccc;MMM.;cccccccccccccccc'   WM: kwin_wayland_wr kwin_wayland 
ccccc;MMo;ccccc;MMW.;ccccccccccccccc;    Theme: Breeze [GTK2/3] 
ccccc;0MNc.ccc.xMMd;ccccccccccccccc;     Icons: breeze-dark [GTK2/3] 
cccccc;dNMWXXXWM0:;cccccccccccccc:,      Cursor: breeze_cursors [GTK2/3] 
cccccccc;.:odl:.;cccccccccccccc:,.       Terminal: konsole 
:cccccccccccccccccccccccccccc:'.         Terminal Font: 终端更纱黑体-简 Nerd 10 
.:cccccccccccccccccccccc:;,..            CPU: 12th Gen Intel i7-12700H (20) @ 4.6GHz 
  '::cccccccccccccc::;,.                 GPU: NVIDIA Geforce RTX 3070 Ti Laptop GPU 
                                         GPU: Intel Alder Lake-P 
                                         Memory: 5.31 GiB / 31.03 GiB (17%) 
                                         Network: Wifi6 
                                         BIOS: American Megatrends International, LLC. 2.11 (03/27/2023) 
                                         Disk (/): 11G / 19G (59%) 
                                         Battery1: 60% [Not charging] 
                                         Font: Noto Sans, 10 [GTK2/3] 
                                         Song: Unknown Artist - Unknown Album - Unknown Song 
                                         Music Player: plasma-browser-integration 
                                         Local IP: 192.168.31.86 
                                         Users: carter 
                                         Locale: zh_CN.UTF-8
```

Output of fastfetch

```
             .',;::::;,'.                 carter@fedora
         .';:cccccccccccc:;,.             -------------
      .;cccccccccccccccccccccc;.          OS: Fedora Linux 38 (Workstation Edition) x86_64
    .:cccccccccccccccccccccccccc:.        Host: Raider GE76 12UGS (REV:1.0)
  .;ccccccccccccc;.:dddl:.;ccccccc;.      Kernel: 6.3.8-200.fc38.x86_64
 .:ccccccccccccc;OWMKOOXMWd;ccccccc:.     Uptime: 1 hour, 7 mins
.:ccccccccccccc;KMMc;cc;xMMc;ccccccc:.    Packages: 2532 (rpm), 10 (brew)
,cccccccccccccc;MMM.;cc;;WW:;cccccccc,    Shell: fish 3.6.1
:cccccccccccccc;MMM.;cccccccccccccccc:    Editor: NVIM v0.9.1
:ccccccc;oxOOOo;MMM000k.;cccccccccccc:    Display (eDP-1): 2560x1440 @ 240Hz (as 1280x720)
cccccc;0MMKxdd:;MMMkddc.;cccccccccccc;    DE: KDE Plasma 5.27.5
ccccc;XMO';cccc;MMM.;cccccccccccccccc'    WM: KWin (Wayland)
ccccc;MMo;ccccc;MMW.;ccccccccccccccc;     Theme: Breeze (Dark) [QT], Breeze [GTK3]
ccccc;0MNc.ccc.xMMd;ccccccccccccccc;      Icons: breeze-dark [QT], breeze-dark [GTK3/4]
cccccc;dNMWXXXWM0:;cccccccccccccc:,       Cursor: breeze (24px)
cccccccc;.:odl:.;cccccccccccccc:,.        Terminal: konsole 23.4.2
ccccccccccccccccccccccccccccc:'.          Terminal Font: 终端更纱黑体-简 Nerd (10pt)
:ccccccccccccccccccccccc:;,..             CPU: 12th Gen Intel(R) Core(TM) i7-12700H (20) @ 4.6 GHz
 ':cccccccccccccccc::;,.                  GPU 1: Intel Alder Lake-P Integrated Graphics Controller
                                          GPU 2: NVIDIA Geforce RTX 3070 Ti Laptop GPU
                                          Memory: 4.95 GiB / 31.04 GiB (15%)
                                          Bluetooth: WH-1000XM5
                                          Wifi: Xiaomi_3F3D_5G - 802.11ax (Wi-Fi 6) - WPA2
                                          Bios: 2.11 (E17K4IMS.20B)
                                          Disk (/): 11.06 GiB / 18.56 GiB (59%) - btrfs
                                          Disk (/run/media/carter/Windows): 833 GiB / 934 GiB (89%) - ntfs3 [External]
                                          Battery: 60% [Not charging]
                                          Font: Noto Sans (10pt) [QT], Noto Sans (10pt) [GTK3/4]
                                          Media: 李丽芬 - 爱江山更爱美人
                                          Media Player: lx-music-desktop
                                          Local IP (wlo1): 192.168.31.86
                                          Users: carter
                                          Locale: zh_CN.UTF-8
```

Note

1. neowofetch failed to detect the refresh rate of my monitor
1. neowofetch failed to detect my bluetooth headset, though enabled in the config file
1. neowofetch failed to detect song and player
1. neowofetch can only detect GTK themes, while fastfetch detect both GTK and Qt themes
1. neowofetch's network module shows `Wifi6`, which seems not very useful, while fastfetch shows connected WIFI SSID and encryption protocol. However fastfetch doesn't support network type other than Wifi.