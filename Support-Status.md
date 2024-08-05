All categories not listed here should work without needing a specific implementation.

### Available Modules
<!-- fastfetch --list-modules -->
```
1)  Battery       : Print battery capacity, status, etc
2)  Bios          : Print information of 1st-stage bootloader (name, version, release date, etc)
3)  Bluetooth     : List (connected) bluetooth devices
4)  BluetoothRadio: List bluetooth radios width supported version and vendor
5)  Board         : Print motherboard name and other info
6)  Bootmgr       : Print information of 2nd-stage bootloader (name, firmware, etc)
7)  Break         : Print a empty line
8)  Brightness    : Print current brightness level of your monitors
9)  Camera        : Print available cameras
10) Chassis       : Print chassis type (desktop, laptop, etc)
11) Command       : Running custom shell scripts
12) Colors        : Print some colored blocks
13) CPU           : Print CPU name, frequency, etc
14) CPUCache      : Print CPU cache sizes
15) CPUUsage      : Print CPU usage. Costs some time to collect data
16) Cursor        : Print cursor style name
17) Custom        : Print a custom string, with or without key
18) DateTime      : Print current date and time
19) DE            : Print desktop environment name
20) Display       : Print resolutions, refresh rates, etc
21) Disk          : Print partitions, space usage, file system, etc
22) DiskIO        : Print physical disk I/O throughput
23) DNS           : Print configured DNS servers
24) Editor        : Print information of the default editor ($VISUAL or $EDITOR)
25) Font          : Print system font name
26) Gamepad       : List connected gamepads
27) GPU           : Print GPU names, graphic memory size, type, etc
28) Host          : Print product name of your computer
29) Icons         : Print icon style name
30) InitSystem    : Print init system (pid 1) name and version
31) Kernel        : Print system kernel version
32) LM            : Print login manager (desktop manager) name and version
33) Loadavg       : Print system load averages
34) Locale        : Print system locale name
35) LocalIp       : List local IP addresses (v4 or v6), MAC addresses, etc
36) Media         : Print playing song name
37) Memory        : Print system memory usage info
38) Monitor       : Print connected physical monitor information
39) NetIO         : Print network I/O throughput
40) OpenCL        : Print highest OpenCL version supported by the GPU
41) OpenGL        : Print highest OpenGL version supported by the GPU
42) OS            : Print operating system name and version
43) Packages      : List installed package managers and count of installed packages
44) PhysicalDisk  : Print physical disk information
45) PhysicalMemory: Print system physical memory devices
46) Player        : Print music player name
47) PowerAdapter  : Print power adapter name and charging watts
48) Processes     : Count running processes
49) PublicIp      : Print your public IP address, etc
50) Separator     : Print a separator line
51) Shell         : Print current shell name and version
52) Sound         : Print sound devices, volume, etc
53) Swap          : Print swap (paging file) space usage
54) Terminal      : Print current terminal name and version
55) TerminalFont  : Print font name and size used by current terminal
56) TerminalSize  : Print current terminal size
57) TerminalTheme : Print current terminal theme (foreground and background colors)
58) Title         : Print title, which contains your user name, hostname
59) Theme         : Print current theme of desktop environment
60) Uptime        : Print how long system has been running
61) Users         : Print users currently logged in
62) Version       : Print Fastfetch version
63) Vulkan        : Print highest Vulkan version supported by the GPU
64) Wallpaper     : Print image file path of current wallpaper
65) Weather       : Print weather information
66) WM            : Print window manager name and version
67) Wifi          : Print connected Wi-Fi info (SSID, connection and security protocol)
68) WMTheme       : Print current theme of window manager
```

Worth noting that not all modules are supported on all platforms.

### Builtin logos
<!-- a = `Content of src/logo/builtin.c`; Object.entries(Object.groupBy(a.split('\n').map(x => x.trim()).filter(x => x.startsWith('// ')).map(x => x.slice(3)).filter(x => x != 'LAST').sort((a, b)=>a.toUpperCase().localeCompare(b.toUpperCase())), x => x[0].toUpperCase())).map(([key, arr]) => `${key}: ${arr.join(', ')}`).join('\n') -->
```
A: Adélie, AerOS, Afterglow, AIX, AlmaLinux, Alpine, Alpine2Small, AlpineSmall, Alter, Amazon, AmazonLinux, AmogOS, Anarchy, Android, AndroidSmall, Antergos, Antix, AoscOS, AoscOS_old, AoscOsRetro, AoscOsRetro_small, Aperture, Apple, AppleSmall, Apricity, Arch, Arch2, ArchBox, Archcraft, Archcraft2, Archlabs, ArchSmall, ArchStrike, ArcoLinux, ArcoLinuxSmall, Armbian, Armbian2, ArseLinux, Artix, Artix2Small, ArtixSmall, Arya, Asahi, Asahi2, Aster, AsteroidOS, AstOS, Astra, Ataraxia, Athena, Azos
B: Bedrock, BigLinux, Bitrig, BlackArch, BlackMesa, BlackPanther, BLAG, BlankOn, BlueLight, Bodhi, Bonsai, BSD, BunsenLabs
C: CachyOS, CachyOSSmall, Calculate, CalinixOS, CalinixOSSmall, Carbs, CBL-Mariner, CelOS, Center, CentOS, CentOSSmall, Cereus, Chakra, ChaletOS, Chapeau, Chimera Linux, ChonkySealOS, Chrom, Cleanjaro, CleanjaroSmall, ClearLinux, ClearOS, Clover, Cobalt, Condres, ContainerLinux, CRUX, CRUXSmall, CrystalLinux, Cucumber, CuerdOS, CutefishOS, CuteOS, CyberOS, cycledream
D: DahliaOS, DarkOS, Debian, DebianSmall, Deepin, DesaOS, Devuan, DevuanSmall, DietPi, DracOS, DragonFly, DragonFlyOld, DragonFlySmall, DraugerOS, Droidian
E: Elbrus, Elementary, ElementarySmall, Elive, EncryptOS, Endeavour, EndeavourSmall, Endless, Enso, EshanizedOS, EuroLinux, evolinx, EvolutionOS, EvolutionOS_old, EvolutionOSSmall, Exherbo, ExodiaPredator
F: Fedora, FedoraAsahiRemix, FedoraCoreOS, FedoraKinoite, FedoraOld, FedoraSericea, FedoraSilverblue, FedoraSmall, FemboyOS, Feren, Finnix, Floflis, FreeBSD, FreeBSDSmall, FreeMiNT, Frugalware, Funtoo, Furreto
G: GalliumOS, Garuda, GarudaDragon, GarudaSmall, Gentoo, GentooSmall, GhostBSD, Glaucus, GNewSense, GNOME, GNU, GoboLinux, GrapheneOS, Grombyang, Guix, GuixSmall
H: Haiku, HaikuSmall, HamoniKR, HarDClanZ, HardenedBSD, Hash, Huayra, Hybrid, HydroOS, Hyperbola, HyperbolaSmall
I: Iglunix, InstantOS, Interix, IRIX, Ironclad, Itc
J: Januslinux
K: Kaisen, Kali, KaliSmall, KaOS, KDENeon, KernelOS, Kibojoe, KISSLinux, Kogaion, Korora, KrassOS, KSLinux, Kubuntu
L: LainOS, LangitKetujuh, Laxeros, LEDE, LibreELEC, Linspire, Linux, LinuxLight, LinuxLightSmall, LinuxMint, LinuxMintOld, LinuxMintSmall, LinuxSmall, Live_Raizo, LMDE, Loc-OS, Lunar
M: MacOS, MacOS2, MacOS2Small, MacOS3, MacOSSmall, Mageia, MageiaSmall, MagpieOS, MainsailOS, MainsailOSSmall, Mandriva, Manjaro, ManjaroSmall, MassOS, MatuusOS, MaUI, Mauna, Meowix, Mer, Minix, Mint, MintOld, MintSmall, MiracleLinux, MOS, Msys2, MX, MX2, MXSmall
N: Namib, Nekos, Neptune, NetBSD, NetRunner, Nitrux, NixOS, NixOSOld, NixOsOldSmall, NixOSSmall, Nobara, NomadBSD, Nurunner, NuTyX
O: Obarun, OBRevenge, OmniOS, OpenBSD, OpenBSDSmall, OpenEuler, OpenIndiana, OpenKylin, OpenMamba, OpenMandriva, OpenStage, OpenSuse, OpenSuseLeap, openSuseMicroOS, openSUSESlowroll, OpenSuseSmall, OpenSuseTumbleweed, OpenWrt, OPNsense, Oracle, Orchid, OrchidSmall, OS_Elbrus, OSMC, OSX, OSXSmall
P: PacBSD, Panwah, Parabola, ParabolaSmall, Parch, Pardus, Parrot, Parsix, PCBSD, PCLinuxOS, PearOS, Pengwin, Pentoo, Peppermint, Peropesis, PhyOS, PikaOS, Pisi, PNMLinux, Pop, PopSmall, Porteus, PostMarketOS, PostMarketOSSmall, Proxmox, PuffOS, Puppy, PureOS, PureOSSmall
Q: Q4OS, Qubes, Qubyt, Quibian, Quirinux
R: Radix, Raspbian, RaspbianSmall, RavynOS, Reborn, RebornSmall, RedCore, RedHatEnterpriseLinux, RedHatEnterpriseLinux_old, RedOS, RedOS small, RedstarOS, Refracted Devuan, Regata, Regolith, RhaymOS, RockyLinux, RockyLinuxSmall, RosaLinux
S: Sabayon, Sabotage, Sailfish, SalentOS, SalientOS, Salix, SambaBOX, Sasanqua, Scientific, Semc, Septor, Serene, SharkLinux, ShastraOS, Siduction, SkiffOS, Slackel, Slackware, SlackwareSmall, Slitaz, SmartOS, Soda, Solaris, SolarisSmall, Solus, Source Mage, Sparky, SpoinkOS, Star, SteamOS, StockLinux, Sulin, Suse, SuseSmall, Swagarch
T: T2, Tails, Tatra, TeArch, TileOS, TorizonCore, Trisquel, TuxedoOS, Twister
U: Ubuntu, Ubuntu2Old, Ubuntu2Small, UbuntuBudgie, UbuntuCinnamon, UbuntuGNOME, UbuntuKde, UbuntuKylin, UbuntuMate, UbuntuOld, UbuntuSmall, UbuntuStudio, UbuntuSway, UbuntuTouch, UbuntuUnity, Ultramarine, Univalent, Univention, UOS, UrukOS, Uwuntu
V: Vanilla, Venom, VenomSmall, Vnux, Void, VoidSmall, Vzlinux
W: WiiLinuxNgx, Windows, Windows11, Windows11Small, Windows8, Windows95
X: Xenia, Xferience
Y: YiffOS
Z: Z/OS, Zorin
```

Run `fastfetch --print-logos` to print them

### Package managers
```
am, apk, brew, Chocolatey, dpkg, emerge, eopkg, Flatpak, Guix, lpkg, lpkgbuild, MacPorts, nix, opkg, Pacman, paludis, pkg, pkgtool, rpm, scoop, Snap, sorcery, winget (disabled by default), xbps
```

### WM themes
```
DWM (Windows), KWin, Marco, Muffin, Mutter, Openbox (LXDE, LXQT & without DE), Quartz Compositor (macOS), XFWM
```

### DE versions
```
Budgie, Cinnamon, Gnome, KDE Plasma, LXQt, Mate, XFCE4
```

### Terminal fonts
```
Alacritty, Apple Terminal, ConEmu, Deepin Terminal, foot, Gnome Console, Gnome Terminal, iTerm2, Kitty, Konsole, LXTerminal, MATE Terminal, mintty, QTerminal, st, Tabby, Terminator, Termux, Tilix, TTY, Warp, WezTerm, Windows Terminal, XFCE4 Terminal, Yakuake
```
