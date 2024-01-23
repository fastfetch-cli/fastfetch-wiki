All categories not listed here should work without needing a specific implementation.

### Available Modules
<!-- fastfetch --list-modules -->
```
1)  Battery       : Print battery capacity, status, etc
2)  Bios          : Print BIOS name, version, release date, etc
3)  Bluetooth     : List bluetooth devices
4)  Board         : Print mather board name and other info
5)  Break         : Print a empty line
6)  Brightness    : Print current brightness level of your monitors
7)  Chassis       : Print chassis type (desktop, laptop, etc)
8)  Command       : Running custom shell scripts
9)  Colors        : Print some colored blocks
10) CPU           : Print CPU name, frequency, etc
11) CPUUsage      : Print CPU usage. Costs some time to collect data
12) Cursor        : Print cursor style name
13) Custom        : Print a custom string, with or without key
14) DateTime      : Print current date and time
15) DE            : Print desktop environment name
16) Display       : Print resolutions, refresh rates, etc
17) Disk          : Print partitions, space usage, disk type, etc
18) DiskIO        : Print physical disk I/O throughput
19) Font          : Print system font name
20) Gamepad       : List connected gamepads
21) GPU           : Print GPU names, graphic memory size, type, etc
22) Host          : Print product name of your computer
23) Icons         : Print icon style name
24) Kernel        : Print system kernel version
25) LM            : Print login manager (desktop manager) name and version
26) Locale        : Print system locale name
27) LocalIp       : List local IP addresses (v4 or v6), MAC addresses, etc
28) Media         : Print playing song name
29) Memory        : Print system memory usage info
30) Monitor       : Print connected physical monitor information
31) NetIO         : Print network I/O throughput
32) OpenCL        : Print highest OpenCL version supported by the GPU
33) OpenGL        : Print highest OpenGL version supported by the GPU
34) OS            : Print operating system name and version
35) Packages      : List installed package managers and count of installed packages
36) PhysicalDisk  : Print physical disk information
37) Player        : Print music player name
38) PowerAdapter  : Print power adapter name and charging watts
39) Processes     : Count running processes
40) PublicIp      : Print your public IP address, etc
41) Separator     : Print a separator line
42) Shell         : Print current shell name and version
43) Sound         : Print sound devices, volume, etc
44) Swap          : Print swap (paging file) space usage
45) Terminal      : Print current terminal name and version
46) TerminalFont  : Print font name and size used by current terminal
47) TerminalSize  : Print current terminal size
48) TerminalTheme : Print current terminal theme (foreground and background colors)
49) Title         : Print title, which contains your user name, hostname
50) Theme         : Print current theme of desktop environment
51) Uptime        : Print how long system has been running
52) Users         : Print users currently logged in
53) Version       : Print Fastfetch version
54) Vulkan        : Print highest Vulkan version supported by the GPU
55) Wallpaper     : Print image file path of current wallpaper
56) Weather       : Print weather information
57) WM            : Print window manager name and version
58) Wifi          : Print connected Wi-Fi info (SSID, connection and security protocol)
59) WMTheme       : Print current theme of window manager
```

### Builtin logos
<!-- Object.entries(Object.groupBy(`Content of src/logo/builtin.c`.split('\n').map(x => x.trim()).filter(x => x.startsWith('// ')).map(x => x.slice(3)).filter(x => x != 'LAST').sort((a, b)=>a.toUpperCase().localeCompare(b.toUpperCase())), x => x[0].toUpperCase())).map(([key, arr]) => `${key}: ${arr.join(', ')}`).join('\n') -->
```
A: Adélie, AerOS, Afterglow, AIX, AlmaLinux, Alpine, Alpine2Small, AlpineSmall, Alter, Amazon, AmazonLinux, AmogOS, Anarchy, Android, AndroidSmall, Antergos, Antix, AoscOS, AoscOS_old, AoscOsRetro, AoscOsRetro_small, Aperture, Apple, AppleSmall, Apricity, Arch, Arch2, ArchBox, Archcraft, Archcraft2, Archlabs, ArchSmall, ArchStrike, ArcoLinux, ArcoLinuxSmall, ArseLinux, Artix, Artix2Small, ArtixSmall, Arya, Asahi, Aster, AsteroidOS, AstOS, Astra, Ataraxia, Athena, Azos
B: Bedrock, BigLinux, Bitrig, BlackArch, BlackMesa, BlackPanther, BLAG, BlankOn, BlueLight, Bodhi, Bonsai, BSD, BunsenLabs
C: CachyOS, CachyOSSmall, Calculate, CalinixOS, CalinixOSSmall, Carbs, CBL-Mariner, CelOS, Center, CentOS, CentOSSmall, Chakra, ChaletOS, Chapeau, Chimera Linux, ChonkySealOS, Chrom, Cleanjaro, CleanjaroSmall, ClearLinux, ClearOS, Clover, Cobalt, Condres, ContainerLinux, CRUX, CRUXSmall, CrystalLinux, Cucumber, CutefishOS, CuteOS, CyberOS, cycledream
D: Dahlia, DarkOS, Debian, DebianSmall, Deepin, DesaOS, Devuan, DevuanSmall, DietPi, DracOS, DragonFly, DragonFlyOld, DragonFlySmall, Drauger, Droidian
E: Elbrus, Elementary, ElementarySmall, Elive, EncryptOS, Endeavour, EndeavourSmall, Endless, Enso, EshanizedOS, EuroLinux, evolinx, EvolutionOS, EvolutionOS_old, EvolutionOSSmall, Exherbo, ExodiaPredator
F: Fedora, FedoraOld, FedoraSmall, FemboyOS, Feren, Finnix, Floflis, FreeBSD, FreeBSDSmall, FreeMiNT, Frugalware, Funtoo
G: GalliumOS, Garuda, GarudaDragon, GarudaSmall, Gentoo, GentooSmall, GhostBSD, Glaucus, GNewSense, Gnome, GNU, GoboLinux, GrapheneOS, Grombyang, Guix, GuixSmall
H: Haiku, HaikuSmall, HamoniKR, HarDClanZ, HardenedBSD, Hash, Huayra, Hybrid, HydroOS, Hyperbola, HyperbolaSmall
I: Iglunix, InstantOS, Interix, IRIX, Ironclad, Itc
J: Januslinux
K: Kaisen, Kali, KaliSmall, KaOS, KDENeon, Kibojoe, KISSLinux, Kogaion, Korora, KrassOS, KSLinux, Kubuntu
L: LainOS, LangitKetujuh, Laxeros, LEDE, LibreELEC, Linspire, Linux, LinuxLight, LinuxLightSmall, LinuxMint, LinuxMintOld, LinuxMintSmall, LinuxSmall, Live_Raizo, LMDE, Lunar
M: MacOS, MacOS2, MacOS2Small, MacOSSmall, Mageia, MageiaSmall, MagpieOS, MainsailOS, MainsailOSSmall, Mandriva, Manjaro, ManjaroSmall, MassOS, MatuusOS, MaUI, Meowix, Mer, Minix, Mint, MintOld, MintSmall, MiracleLinux, MOS, Msys2, MX, MXSmall
N: Namib, Nekos, Neptune, NetBSD, NetRunner, Nitrux, NixOS, NixOSOld, NixOsOldSmall, NixOSSmall, Nobara, NomadBSD, Nurunner, NuTyX
O: Obarun, OBRevenge, OmniOS, OpenBSD, OpenBSDSmall, OpenEuler, OpenIndiana, OpenKylin, OpenMamba, OpenMandriva, OpenStage, OpenSuse, OpenSuseLeap, OpenSuseSmall, OpenSuseTumbleweed, OpenWrt, OPNsense, Oracle, Orchid, OrchidSmall, OS_Elbrus, OSMC, OSX, OSXSmall
P: PacBSD, Panwah, Parabola, ParabolaSmall, Parch, Pardus, Parrot, Parsix, PCBSD, PCLinuxOS, PearOS, Pengwin, Pentoo, Peppermint, Peropesis, PhyOS, Pisi, PNMLinux, Pop, PopSmall, Porteus, PostMarketOS, PostMarketOSSmall, Proxmox, PuffOS, Puppy, PureOS, PureOSSmall
Q: Q4OS, Qubes, Qubyt, Quibian
R: Radix, Raspbian, RaspbianSmall, RavynOS, Reborn, RebornSmall, RedCore, RedHatEnterpriseLinux, RedHatEnterpriseLinux_old, RedstarOS, Refracted Devuan, Regata, Regolith, RhaymOS, RockyLinux, RockyLinuxSmall, RosaLinux
S: Sabayon, Sabotage, Sailfish, SalentOS, SalientOS, Salix, SambaBOX, Sasanqua, Scientific, Semc, Septor, Serene, SharkLinux, ShastraOS, Siduction, SkiffOS, Slackel, Slackware, SlackwareSmall, Slitaz, SmartOS, Soda, Solaris, SolarisSmall, Solus, SourceMage, Sparky, Star, SteamOS, StockLinux, Sulin, Suse, SuseSmall, Swagarch
T: T2, Tails, TeArch, TileOS, TorizonCore, Trisquel, TuxedoOS, Twister
U: Ubuntu, Ubuntu2Old, Ubuntu2Small, UbuntuBudgie, UbuntuCinnamon, UbuntuGnome, UbuntuKde, UbuntuKylin, UbuntuMate, UbuntuOld, UbuntuSmall, UbuntuStudio, UbuntuSway, UbuntuTouch, UbuntuUnity, Ultramarine, Univalent, Univention, UOS, UrukOS, Uwuntu
V: Vanilla, Venom, VenomSmall, Vnux, Void, VoidSmall, Vzlinux
W: WiiLinuxNgx, Windows, Windows11, Windows11Small, Windows8, Windows95
X: Xenia, Xferience
Y: YiffOS
Z: Zorin
```

Run `fastfetch --print-logos` to print them

### Package managers
```
apk, brew, Chocolatey, dpkg, emerge, eopkg, Flatpak, MacPorts, nix, opkg, Pacman, paludis, pkg, pkgtool, rpm, scoop, Snap, winget, xbps
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
