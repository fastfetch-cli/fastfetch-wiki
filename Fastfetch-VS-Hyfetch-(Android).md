Following benchmarks were performed using [hyperfine](https://github.com/sharkdp/hyperfine) with hyfetch [1.4.4](https://github.com/hykilpikonna/hyfetch/releases/tag/1.4.4) and fastfetch [34d3021](https://github.com/LinusDierheimer/fastfetch/commit/34d3021b803b75ca78f1203cb78648d3a3795c3d), running on Android (12). All supported modules except Public IP (because it uses Network) in hyfetch were enabled, and fastfetch was set up with the equivalent modules enabled.

```
hyperfine -w 3 'hyfetch' './fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:terminal:terminalfont:cpu:gpu:memory:disk:localip:users:locale --nocache'
Benchmark 1: hyfetch
  Time (mean ± σ):     692.2 ms ±  20.4 ms    [User: 444.0 ms, System: 253.8 ms]
  Range (min … max):   636.0 ms … 705.0 ms    10 runs

Benchmark 2: ./fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:terminal:terminalfont:cpu:gpu:memory:disk:localip:users:locale --nocache
  Time (mean ± σ):      68.4 ms ±   2.9 ms    [User: 25.1 ms, System: 37.8 ms]
  Range (min … max):    62.3 ms …  76.1 ms    41 runs

Summary
  './fastfetch -s title:separator:os:host:kernel:uptime:packages:shell:terminal:terminalfont:cpu:gpu:memory:disk:localip:users:locale --nocache' ran
   10.12 ± 0.52 times faster than 'hyfetch'
```

Output of hyfetch

```
         -o          o-            u0_a268@localhost
          +hydNNNNdyh+             -----------------
        +mMMMMMMMMMMMMm+           OS: Android 12 aarch64
      `dMMm:NMMMMMMN:mMMd`         Host: Xiaomi 2201122C
      hMMMMMMMMMMMMMMMMMMh         Kernel: 5.10.43-android12-9-gb8e472053352-ab8059253
  ..  yyyyyyyyyyyyyyyyyyyy  ..     Uptime: 11 hours, 18 mins
.mMMm`MMMMMMMMMMMMMMMMMMMM`mMMm.   Packages: 153 (dpkg), 1 (pm)
:MMMM-MMMMMMMMMMMMMMMMMMMM-MMMM:   Shell: fish 3.5.1
:MMMM-MMMMMMMMMMMMMMMMMMMM-MMMM:   Terminal: Termux 0.118.0
:MMMM-MMMMMMMMMMMMMMMMMMMM-MMMM:   CPU: (8) @ 1.7GHz
:MMMM-MMMMMMMMMMMMMMMMMMMM-MMMM:   Memory: 4.70 GiB / 10.95 GiB (42%)
-MMMM-MMMMMMMMMMMMMMMMMMMM-MMMM-   Disk (/): 3.7G / 3.7G (100%)
 +yy+ MMMMMMMMMMMMMMMMMMMM +yy+    Local IP: 172.16.134.70
      mMMMMMMMMMMMMMMMMMMm         Locale: en_US.UTF-8
      `/++MMMMh++hMMMM++/`
          MMMMo  oMMMM
          MMMMo  oMMMM
          oNMm-  -mMNs
```

Output of fastfetch

```
         -o          o-             u0_a268@localhost
          +hydNNNNdyh+              -----------------
        +mMMMMMMMMMMMMm+            OS: Android 12 aarch64
      `dMMm:NMMMMMMN:mMMd`          Host: Xiaomi 2201122C
      hMMMMMMMMMMMMMMMMMMh          Kernel: 5.10.43-android12-9-gb8e472053352-ab8059253
  ..  yyyyyyyyyyyyyyyyyyyy  ..      Uptime: 11 hours, 19 mins
.mMMm`MMMMMMMMMMMMMMMMMMMM`mMMm.    Packages: 153 (dpkg)
:MMMM-MMMMMMMMMMMMMMMMMMMM-MMMM:    Shell: fish 3.5.1
:MMMM-MMMMMMMMMMMMMMMMMMMM-MMMM:    Terminal: Termux
:MMMM-MMMMMMMMMMMMMMMMMMMM-MMMM:    Terminal Font: Sarasa Mono SC Nerd
:MMMM-MMMMMMMMMMMMMMMMMMMM-MMMM:    CPU: CPU (8) @ 1.7856 GHz
-MMMM-MMMMMMMMMMMMMMMMMMMM-MMMM-    GPU: Adreno (TM) 730
 +yy+ MMMMMMMMMMMMMMMMMMMM +yy+     Memory: 4.73 GiB / 10.92 GiB (43%)
      mMMMMMMMMMMMMMMMMMMm          Disk (/): 3.75 GiB / 3.75 GiB (100%)
      `/++MMMMh++hMMMM++/`          Disk (/storage/emulated): 181 GiB / 223 GiB (81%)
          MMMMo  oMMMM              Local IP (wlan0): 172.16.134.70
          MMMMo  oMMMM              Locale: en_US.UTF-8
          oNMm-  -mMNs
```