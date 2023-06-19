Following benchmarks were performed using [Measure-Command](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/measure-command?view=powershell-7.3) with winfetch [9cc0ebe](https://github.com/lptstr/winfetch/commit/9cc0ebea77a7ace0f515e8c3e83b7f599329cb29) and fastfetch [0540930](https://github.com/fastfetch-cli/fastfetch/commit/054093025e9b9773853151665ee1cd635f0e7953), running on Windows 11 22H2. All winfetch modules except `custom_time`, `ps_pkgs`, `weather`, `public_ip` were enabled, and fastfetch was set up with the equivalent info enabled.

```
fastfetch\build on  dev [$] via C v16.0.4-clang via △ v3.26.4
❯ (Measure-Command { ~\winfetch.ps1 }).TotalSeconds
1.8527376

fastfetch\build on  dev [$] via C v16.0.4-clang via △ v3.26.4
❯ (Measure-Command { ~\winfetch.ps1 }).TotalSeconds
1.0900432

fastfetch\build on  dev [$] via C v16.0.4-clang via △ v3.26.4
❯ (Measure-Command { ~\winfetch.ps1 }).TotalSeconds
1.1271545

fastfetch\build on  dev [$] via C v16.0.4-clang via △ v3.26.4
❯ (Measure-Command { ~\winfetch.ps1 }).TotalSeconds
1.2408277

fastfetch\build on  dev [$] via C v16.0.4-clang via △ v3.26.4
❯ (Measure-Command { ~\winfetch.ps1 }).TotalSeconds
1.2407647

fastfetch\build on  dev [$] via C v16.0.4-clang via △ v3.26.4
❯ (Measure-Command {.\fastfetch.exe -s title:separator:os:host:kernel:board:uptime:packages:shell:display:terminal:wmtheme:cpu:gpu:cpuusage:processes:memory:disk:battery:locale:localip:break:colors --localip-name-prefix WLAN}).TotalSeconds
0.116345

fastfetch\build on  dev [$] via C v16.0.4-clang via △ v3.26.4
❯ (Measure-Command {.\fastfetch.exe -s title:separator:os:host:kernel:board:uptime:packages:shell:display:terminal:wmtheme:cpu:gpu:cpuusage:processes:memory:disk:battery:locale:localip:break:colors --localip-name-prefix WLAN}).TotalSeconds
0.3323539

fastfetch\build on  dev [$] via C v16.0.4-clang via △ v3.26.4
❯ (Measure-Command {.\fastfetch.exe -s title:separator:os:host:kernel:board:uptime:packages:shell:display:terminal:wmtheme:cpu:gpu:cpuusage:processes:memory:disk:battery:locale:localip:break:colors --localip-name-prefix WLAN}).TotalSeconds
0.3438836

fastfetch\build on  dev [$] via C v16.0.4-clang via △ v3.26.4
❯ (Measure-Command {.\fastfetch.exe -s title:separator:os:host:kernel:board:uptime:packages:shell:display:terminal:wmtheme:cpu:gpu:cpuusage:processes:memory:disk:battery:locale:localip:break:colors --localip-name-prefix WLAN}).TotalSeconds
0.3486099

fastfetch\build on  dev [$] via C v16.0.4-clang via △ v3.26.4
❯ (Measure-Command {.\fastfetch.exe -s title:separator:os:host:kernel:board:uptime:packages:shell:display:terminal:wmtheme:cpu:gpu:cpuusage:processes:memory:disk:battery:locale:localip:break:colors --localip-name-prefix WLAN}).TotalSeconds
0.1061817
```

|     | winfetch  | fastfetch |
| --- | --------- | --------- |
| 1   | 1.8527376 | 0.1163450 |
| 2   | 1.0900432 | 0.3323539 |
| 3   | 1.1271545 | 0.3438836 |
| 4   | 1.2408277 | 0.3486099 |
| 5   | 1.2407647 | 0.1061817 |
| AVG | 1.3103055 | 0.2494748 |

---

Output of winfetch

```
 lllllllllllllll   lllllllllllllll  zhang@MSIGE76
 lllllllllllllll   lllllllllllllll  -------------
 lllllllllllllll   lllllllllllllll  OS: Windows 11 家庭中文版 [64 位]
 lllllllllllllll   lllllllllllllll  Host: Micro-Star International Co., Ltd. Raider GE76 12UGS
 lllllllllllllll   lllllllllllllll  Kernel: 10.0.22631.0
 lllllllllllllll   lllllllllllllll  Motherboard: Micro-Star International Co., Ltd. MS-17K4
 lllllllllllllll   lllllllllllllll  Uptime: 32 minutes
                                    Packages: 36 (scoop)
 lllllllllllllll   lllllllllllllll  Shell: PowerShell v7.3.4
 lllllllllllllll   lllllllllllllll  Resolution: 1707x960
 lllllllllllllll   lllllllllllllll  Terminal: Windows Terminal
 lllllllllllllll   lllllllllllllll  Theme: themeA (System: Dark, Apps: Dark)
 lllllllllllllll   lllllllllllllll  CPU: 12th Gen Intel(R) Core(TM) i7-12700H @ 2.688GHz
 lllllllllllllll   lllllllllllllll  GPU: Intel(R) Iris(R) Xe Graphics
 lllllllllllllll   lllllllllllllll  GPU: NVIDIA GeForce RTX 3070 Ti Laptop GPU
                                    CPU Usage: 3% (274 processes)
                                    Memory: 9.82 GiB / 31.71 GiB (30%)
                                    Disk (C:): 836 GiB / 933 GiB (89%)
                                    Battery: 54% (Plugged in)
                                    Locale: China -
                                    Local IP: 192.168.1.8
```

Note: my linux partition is lost.

Output of fastfetch

```
                                        zhang@MSIGE76
/////////////////  /////////////////    --------------
/////////////////  /////////////////    OS: Windows 11 (家庭中文版) x86_64
/////////////////  /////////////////    Host: Raider GE76 12UGS (REV:1.0)
/////////////////  /////////////////    Kernel: 10.0.22631.1900 (22H2)
/////////////////  /////////////////    Board: MS-17K4 (REV:1.0)
/////////////////  /////////////////    Uptime: 33 mins
/////////////////  /////////////////    Packages: 36 (scoop)
/////////////////  /////////////////    Shell: PowerShell 7.3.4
                                        Display: 2560x1440 @ 240Hz (as 1707x960)
/////////////////  /////////////////    Terminal: Windows Terminal Preview 1.18.1462.0
/////////////////  /////////////////    WM Theme: ThemeA - #680081 (System: Dark, Apps: Dark)
/////////////////  /////////////////    CPU: 12th Gen Intel(R) Core(TM) i7-12700H (20) @ 2.688 GHz
/////////////////  /////////////////    GPU 1: Intel(R) Iris(R) Xe Graphics (1.00 GiB)
/////////////////  /////////////////    GPU 2: NVIDIA GeForce RTX 3070 Ti Laptop GPU (8.00 GiB)
/////////////////  /////////////////    CPU Usage: 6%
/////////////////  /////////////////    Processes: 277
/////////////////  /////////////////    Memory: 9.98 GiB / 31.71 GiB (31%)
                                        Disk (C:\): 836 GiB / 934 GiB (89%) - NTFS
                                        Disk (D:\): 9.05 GiB / 18.56 GiB (48%) - Btrfs
                                        Battery: 53% [AC Connected]
                                        Locale: Chinese (Simplified)_China.utf8
                                        Local IP (WLAN): 192.168.1.8
```