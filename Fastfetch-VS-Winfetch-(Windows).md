Following benchmarks were performed using [hyperfine](https://github.com/sharkdp/hyperfine) with winfetch [c0bbc26](https://github.com/lptstr/winfetch/commit/c0bbc26c86e4f211383754ee52d39c0270ec8212) and fastfetch [34d3021](https://github.com/LinusDierheimer/fastfetch/commit/34d3021b803b75ca78f1203cb78648d3a3795c3d), running on Windows 11 22H2. All winfetch modules except `custom_time`, `ps_pkgs`, `weather`, `public_ip` were enabled, and fastfetch was set up with the equivalent info enabled.

```
❯ hyperfine -w 3 "pwsh -file .\winfetch.ps1" ".\fastfetch\build\fastfetch.exe -s title:separator:os:kernel:board:uptime:packages:shell:resolution:terminal:wmtheme:cpu:gpu:cpuusage:processes:memory:disk:battery:locale:localip:break:colors --nocache --localip-name-prefix WLAN"
Benchmark 1: pwsh -file .\winfetch.ps1
  Time (mean ± σ):      1.669 s ±  0.126 s    [User: 0.006 s, System: 0.003 s]
  Range (min … max):    1.488 s …  1.847 s    10 runs

Benchmark 2: .\fastfetch\build\fastfetch.exe -s title:separator:os:kernel:board:uptime:packages:shell:resolution:terminal:wmtheme:cpu:gpu:cpuusage:processes:memory:disk:battery:locale:localip:break:colors --nocache --localip-name-prefix WLAN
  Time (mean ± σ):     140.5 ms ±  77.3 ms    [User: 3.6 ms, System: 21.3 ms]
  Range (min … max):    63.5 ms … 305.8 ms    15 runs

Summary
  '.\fastfetch\build\fastfetch.exe -s title:separator:os:kernel:board:uptime:packages:shell:resolution:terminal:wmtheme:cpu:gpu:cpuusage:processes:memory:disk:battery:locale:localip:break:colors --nocache --localip-name-prefix WLAN' ran
   11.88 ± 6.60 times faster than 'pwsh -file .\winfetch.ps1'
```

If running inside of pwsh shell directly, winfetch performs much better. Following benchmarks were performed using [Measure-Command](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/measure-command?view=powershell-7.3).

```
~ via C v12.2.1-gcc
❯ (Measure-Command { .\winfetch.ps1 }).TotalSeconds
0.3080349

~ via C v12.2.1-gcc
❯ (Measure-Command { .\winfetch.ps1 }).TotalSeconds
0.3298625

~ via C v12.2.1-gcc
❯ (Measure-Command { .\winfetch.ps1 }).TotalSeconds
0.2881014

~ via C v12.2.1-gcc
❯ (Measure-Command { .\winfetch.ps1 }).TotalSeconds
0.2670504

~ via C v12.2.1-gcc
❯ (Measure-Command { .\winfetch.ps1 }).TotalSeconds
0.3448253

~ via C v12.2.1-gcc
❯ (Measure-Command { .\fastfetch\build\fastfetch.exe -s title:separator:os:kernel:board:uptime:packages:shell:resolution:terminal:wmtheme:cpu:gpu:processes:cpuusage:memory:disk:battery:locale:localip:break:colors --nocache --localip-name-prefix WLAN }).TotalSeconds
0.1331836

~ via C v12.2.1-gcc
❯ (Measure-Command { .\fastfetch\build\fastfetch.exe -s title:separator:os:kernel:board:uptime:packages:shell:resolution:terminal:wmtheme:cpu:gpu:processes:cpuusage:memory:disk:battery:locale:localip:break:colors --nocache --localip-name-prefix WLAN }).TotalSeconds
0.0707694

~ via C v12.2.1-gcc
❯ (Measure-Command { .\fastfetch\build\fastfetch.exe -s title:separator:os:kernel:board:uptime:packages:shell:resolution:terminal:wmtheme:cpu:gpu:processes:cpuusage:memory:disk:battery:locale:localip:break:colors --nocache --localip-name-prefix WLAN }).TotalSeconds
0.1169886

~ via C v12.2.1-gcc
❯ (Measure-Command { .\fastfetch\build\fastfetch.exe -s title:separator:os:kernel:board:uptime:packages:shell:resolution:terminal:wmtheme:cpu:gpu:processes:cpuusage:memory:disk:battery:locale:localip:break:colors --nocache --localip-name-prefix WLAN }).TotalSeconds
0.1030325

~ via C v12.2.1-gcc
❯ (Measure-Command { .\fastfetch\build\fastfetch.exe -s title:separator:os:kernel:board:uptime:packages:shell:resolution:terminal:wmtheme:cpu:gpu:processes:cpuusage:memory:disk:battery:locale:localip:break:colors --nocache --localip-name-prefix WLAN }).TotalSeconds
0.1071746
```

|     | winfetch  | fastfetch |
| --- | --------- | --------- |
| 1   | 0.3080349 | 0.1331836 |
| 2   | 0.3298625 | 0.0707694 |
| 3   | 0.2881014 | 0.1169886 |
| 4   | 0.2670504 | 0.1030325 |
| 5   | 0.3448253 | 0.0968723 |
| AVG | 0.3075749 | 0.1041693 |

---

Output of winfetch

```
 lllllllllllllll   lllllllllllllll  zhang@MSIGE76
 lllllllllllllll   lllllllllllllll  -------------
 lllllllllllllll   lllllllllllllll  OS: Windows 11 家庭中文版 [64 位]
 lllllllllllllll   lllllllllllllll  Host: Micro-Star International Co., Ltd. Raider GE76 12UGS
 lllllllllllllll   lllllllllllllll  Kernel: 10.0.22623.0
 lllllllllllllll   lllllllllllllll  Motherboard: Micro-Star International Co., Ltd. MS-17K4
 lllllllllllllll   lllllllllllllll  Uptime: 3 days 1 hour 36 minutes
                                    Packages: 23 (scoop)
 lllllllllllllll   lllllllllllllll  Shell: PowerShell v7.3.0
 lllllllllllllll   lllllllllllllll  Resolution: 1707x960
 lllllllllllllll   lllllllllllllll  Terminal: Windows Terminal
 lllllllllllllll   lllllllllllllll  Theme: System - Dark, Apps - Dark
 lllllllllllllll   lllllllllllllll  CPU: 12th Gen Intel(R) Core(TM) i7-12700H @ 2.7GHz
 lllllllllllllll   lllllllllllllll  GPU: Intel(R) Iris(R) Xe Graphics
 lllllllllllllll   lllllllllllllll  GPU: NVIDIA GeForce RTX 3070 Ti Laptop GPU
                                    CPU Usage: 1% (303 processes)
                                    Memory: 11.25 GiB / 31.71 GiB (35%)
                                    Disk (C:): 856 GiB / 933 GiB (91%)
                                    Battery: 51% (Plugged in)
                                    Locale: China -  - English (United States)
                                    Local IP: 192.168.1.7
```

Output of fastfetch

```
                                        zhang@MSIGE76
,,**************/  ///////////////()    -------------
****************/  ///////////////((    OS: Windows 11 (家庭中文版) x86_64
***************//  //////////////(((    Kernel: 10.0.22623.891 (22H2)
**************///  ////////////(((((    Board: MS-17K4
************/////  /////////((((((((    Uptime: 3 days, 1 hour, 37 mins
*********////////  /////((((((((((((    Packages: 23 (scoop)
*******//////////  ///((((((((((((((    Shell: PowerShell 7.3.0.500
****/////////////  (((((((((((((((((    Resolution: 2560x1440 @ 240Hz
                                        Terminal: Windows Terminal
/////////////((((  (((((((((((((((((    WM Theme: Accent Color - Blue, System - Dark, Apps - Dark
////////////(((((  (((((((((((((((((    CPU: 12th Gen Intel(R) Core(TM) i7-12700H (20) @ 2.688 GHz
///////////((((((  (((((((((((((((((    GPU 1: Intel(R) Iris(R) Xe Graphics
/////////((((((((  (((((((((((((((((    GPU 2: NVIDIA GeForce RTX 3070 Ti Laptop GPU
///////((((((((((  (((((((((((((((((    Processes: 303
////(((((((((((((  (((((((((((((((((    CPU Usage: 6.12%
(((((((((((((((((  (((((((((((((((((    Memory: 11.29 GiB / 31.71 GiB (35%)
(((((((((((((((((  (((((((((((((((()    Disk (C:\): 857 GiB / 934 GiB (91%)
                                        Battery: 51% [AC Connected]
                                        Locale: English_United States.utf8
                                        Local IP (WLAN): 192.168.1.7
```