Following benchmarks were performed using [hyperfine](https://github.com/sharkdp/hyperfine) with winfetch [c0bbc26](https://github.com/lptstr/winfetch/commit/c0bbc26c86e4f211383754ee52d39c0270ec8212) and fastfetch [34d3021](https://github.com/LinusDierheimer/fastfetch/commit/34d3021b803b75ca78f1203cb78648d3a3795c3d), running on Windows 11 22H2. All winfetch modules except `custom_time`, `ps_pkgs`, `weather`, `public_ip` were enabled, and fastfetch was set up with the equivalent info enabled.

```
❯ hyperfine -w 3 "pwsh -file .\winfetch.ps1" ".\fastfetch\build\fastfetch.exe -s title:separator:os:host:kernel:board:uptime:packages:shell:resolution:terminal:wmtheme:cpu:gpu:cpuusage:processes:memory:disk:battery:locale:localip:break:colors --nocache --localip-name-prefix WLAN"
Benchmark 1: pwsh -file .\winfetch.ps1
  Time (mean ± σ):      1.879 s ±  0.150 s    [User: 0.002 s, System: 0.003 s]
  Range (min … max):    1.635 s …  2.060 s    10 runs

Benchmark 2: .\fastfetch\build\fastfetch.exe -s title:separator:os:host:kernel:board:uptime:packages:shell:resolution:terminal:wmtheme:cpu:gpu:cpuusage:processes:memory:disk:battery:locale:localip:break:colors --nocache --localip-name-prefix WLAN
  Time (mean ± σ):     163.0 ms ±  98.5 ms    [User: 4.9 ms, System: 14.7 ms]
  Range (min … max):    74.6 ms … 463.7 ms    25 runs

Summary
  '.\fastfetch\build\fastfetch.exe -s title:separator:os:host:kernel:board:uptime:packages:shell:resolution:terminal:wmtheme:cpu:gpu:cpuusage:processes:memory:disk:battery:locale:localip:break:colors --nocache --localip-name-prefix WLAN' ran
   11.53 ± 7.02 times faster than 'pwsh -file .\winfetch.ps1'
```

If running inside of pwsh shell directly, winfetch performs much better. Following benchmarks were performed using [Measure-Command](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/measure-command?view=powershell-7.3).

```
~ via C v12.2.1-gcc
❯ (Measure-Command { .\winfetch.ps1 }).TotalSeconds
0.2845287

~ via C v12.2.1-gcc
❯ (Measure-Command { .\winfetch.ps1 }).TotalSeconds
0.2188932

~ via C v12.2.1-gcc
❯ (Measure-Command { .\winfetch.ps1 }).TotalSeconds
0.2466388

~ via C v12.2.1-gcc
❯ (Measure-Command { .\winfetch.ps1 }).TotalSeconds
0.2306141

~ via C v12.2.1-gcc
❯ (Measure-Command { .\winfetch.ps1 }).TotalSeconds
0.3210739

~ via C v12.2.1-gcc
❯ (Measure-Command { .\winfetch.ps1 }).TotalSeconds
0.2360355

~ via C v12.2.1-gcc
❯ (Measure-Command { .\fastfetch\build\fastfetch.exe -s title:separator:os:host:kernel:board:uptime:packages:shell:resolution:terminal:wmtheme:cpu:gpu:processes:cpuusage:memory:disk:battery:locale:localip:break:colors --nocache --localip-name-prefix WLAN }).TotalSeconds
0.0797149

~ via C v12.2.1-gcc
❯ (Measure-Command { .\fastfetch\build\fastfetch.exe -s title:separator:os:host:kernel:board:uptime:packages:shell:resolution:terminal:wmtheme:cpu:gpu:processes:cpuusage:memory:disk:battery:locale:localip:break:colors --nocache --localip-name-prefix WLAN }).TotalSeconds
0.0898223

~ via C v12.2.1-gcc
❯ (Measure-Command { .\fastfetch\build\fastfetch.exe -s title:separator:os:host:kernel:board:uptime:packages:shell:resolution:terminal:wmtheme:cpu:gpu:processes:cpuusage:memory:disk:battery:locale:localip:break:colors --nocache --localip-name-prefix WLAN }).TotalSeconds
0.0822892

~ via C v12.2.1-gcc
❯ (Measure-Command { .\fastfetch\build\fastfetch.exe -s title:separator:os:host:kernel:board:uptime:packages:shell:resolution:terminal:wmtheme:cpu:gpu:processes:cpuusage:memory:disk:battery:locale:localip:break:colors --nocache --localip-name-prefix WLAN }).TotalSeconds
0.0763098

~ via C v12.2.1-gcc
❯ (Measure-Command { .\fastfetch\build\fastfetch.exe -s title:separator:os:host:kernel:board:uptime:packages:shell:resolution:terminal:wmtheme:cpu:gpu:processes:cpuusage:memory:disk:battery:locale:localip:break:colors --nocache --localip-name-prefix WLAN }).TotalSeconds
0.1324725
```

|     | winfetch  | fastfetch |
| --- | --------- | --------- |
| 1   | 0.2845287 | 0.0797149 |
| 2   | 0.2188932 | 0.0898223 |
| 3   | 0.2466388 | 0.0822892 |
| 4   | 0.3210739 | 0.0763098 |
| 5   | 0.2360355 | 0.1324725 |
| AVG | 0.2614340 | 0.0921217 |

---

Output of winfetch

```
 lllllllllllllll   lllllllllllllll  zhang@MSIGE76
 lllllllllllllll   lllllllllllllll  -------------
 lllllllllllllll   lllllllllllllll  OS: Windows 11 家庭中文版 [64 位]
 lllllllllllllll   lllllllllllllll  Host: Micro-Star International Co., Ltd. Raider GE76 12UGS
 lllllllllllllll   lllllllllllllll  Kernel: 10.0.22623.0
 lllllllllllllll   lllllllllllllll  Motherboard: Micro-Star International Co., Ltd. MS-17K4
 lllllllllllllll   lllllllllllllll  Uptime: 1 day 2 hours 18 minutes
                                    Packages: 28 (scoop)
 lllllllllllllll   lllllllllllllll  Shell: PowerShell v7.3.0
 lllllllllllllll   lllllllllllllll  Resolution: 1707x960
 lllllllllllllll   lllllllllllllll  Terminal: Windows Terminal
 lllllllllllllll   lllllllllllllll  Theme: System - Dark, Apps - Dark
 lllllllllllllll   lllllllllllllll  CPU: 12th Gen Intel(R) Core(TM) i7-12700H @ 2.7GHz
 lllllllllllllll   lllllllllllllll  GPU: Intel(R) Iris(R) Xe Graphics
 lllllllllllllll   lllllllllllllll  GPU: NVIDIA GeForce RTX 3070 Ti Laptop GPU
                                    CPU Usage: 2% (291 processes)
                                    Memory: 9.04 GiB / 31.71 GiB (28%)
                                    Disk (C:): 801 GiB / 933 GiB (85%)
                                    Battery: 54% (Plugged in)
                                    Locale: China -  - English (United States)
                                    Local IP: 192.168.1.7
```

Output of fastfetch

```
                                        zhang@MSIGE76
,,**************/  ///////////////()    -------------
****************/  ///////////////((    OS: Windows 11 (家庭中文版) x86_64
***************//  //////////////(((    Host: Raider GE76 12UGS (REV:1.0)
**************///  ////////////(((((    Kernel: 10.0.22623.1020 (22H2)
************/////  /////////((((((((    Board: MS-17K4
*********////////  /////((((((((((((    Uptime: 1 day, 2 hours, 18 mins
*******//////////  ///((((((((((((((    Packages: 28 (scoop)
****/////////////  (((((((((((((((((    Shell: PowerShell 7.3.0.500
                                        Resolution: 2560x1440 @ 240Hz
/////////////((((  (((((((((((((((((    Terminal: Windows Terminal
////////////(((((  (((((((((((((((((    WM Theme: Accent Color - Blue, System - Dark, Apps - Dark
///////////((((((  (((((((((((((((((    CPU: 12th Gen Intel(R) Core(TM) i7-12700H (20) @ 2.688 GHz
/////////((((((((  (((((((((((((((((    GPU 1: Intel(R) Iris(R) Xe Graphics
///////((((((((((  (((((((((((((((((    GPU 2: NVIDIA GeForce RTX 3070 Ti Laptop GPU
////(((((((((((((  (((((((((((((((((    Processes: 292
(((((((((((((((((  (((((((((((((((((    CPU Usage: 2.91%
(((((((((((((((((  (((((((((((((((()    Memory: 9.06 GiB / 31.71 GiB (28%)
                                        Disk (C:\): 801 GiB / 934 GiB (85%)
                                        Battery: 54% [AC Connected]
                                        Locale: Chinese (Simplified)_China.utf8
                                        Local IP (WLAN): 192.168.1.7
```