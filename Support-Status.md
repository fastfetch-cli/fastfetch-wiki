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
7)  Camera        : Print available cameras
8)  Chassis       : Print chassis type (desktop, laptop, etc)
9)  Command       : Running custom shell scripts
10) Colors        : Print some colored blocks
11) CPU           : Print CPU name, frequency, etc
12) CPUUsage      : Print CPU usage. Costs some time to collect data
13) Cursor        : Print cursor style name
14) Custom        : Print a custom string, with or without key
15) DateTime      : Print current date and time
16) DE            : Print desktop environment name
17) Display       : Print resolutions, refresh rates, etc
18) Disk          : Print partitions, space usage, disk type, etc
19) DiskIO        : Print physical disk I/O throughput
20) Font          : Print system font name
21) Gamepad       : List connected gamepads
22) GPU           : Print GPU names, graphic memory size, type, etc
23) Host          : Print product name of your computer
24) Icons         : Print icon style name
25) Kernel        : Print system kernel version
26) LM            : Print login manager (desktop manager) name and version
27) Locale        : Print system locale name
28) LocalIp       : List local IP addresses (v4 or v6), MAC addresses, etc
29) Media         : Print playing song name
30) Memory        : Print system memory usage info
31) Monitor       : Print connected physical monitor information
32) NetIO         : Print network I/O throughput
33) OpenCL        : Print highest OpenCL version supported by the GPU
34) OpenGL        : Print highest OpenGL version supported by the GPU
35) OS            : Print operating system name and version
36) Packages      : List installed package managers and count of installed packages
37) PhysicalDisk  : Print physical disk information
38) Player        : Print music player name
39) PowerAdapter  : Print power adapter name and charging watts
40) Processes     : Count running processes
41) PublicIp      : Print your public IP address, etc
42) Separator     : Print a separator line
43) Shell         : Print current shell name and version
44) Sound         : Print sound devices, volume, etc
45) Swap          : Print swap (paging file) space usage
46) Terminal      : Print current terminal name and version
47) TerminalFont  : Print font name and size used by current terminal
48) TerminalSize  : Print current terminal size
49) TerminalTheme : Print current terminal theme (foreground and background colors)
50) Title         : Print title, which contains your user name, hostname
51) Theme         : Print current theme of desktop environment
52) Uptime        : Print how long system has been running
53) Users         : Print users currently logged in
54) Version       : Print Fastfetch version
55) Vulkan        : Print highest Vulkan version supported by the GPU
56) Wallpaper     : Print image file path of current wallpaper
57) Weather       : Print weather information
58) WM            : Print window manager name and version
59) Wifi          : Print connected Wi-Fi info (SSID, connection and security protocol)
60) WMTheme       : Print current theme of window manager
```

Worth noting that not all modules are supported on all platforms.

### Builtin logos
<!-- Object.entries(Object.groupBy(`Content of src/logo/builtin.c`.split('\n').map(x => x.trim()).filter(x => x.startsWith('// ')).map(x => x.slice(3)).filter(x => x != 'LAST').sort((a, b)=>a.toUpperCase().localeCompare(b.toUpperCase())), x => x[0].toUpperCase())).map(([key, arr]) => `${key}: ${arr.join(', ')}`).join('\n') -->
```
A: Adélie, AerOS, Afterglow, AIX, AlmaLinux, Alpine, Alpine2Small, AlpineSmall, Alter, Amazon, AmazonLinux, AmogOS, Anarchy, Android, AndroidSmall, Antergos, Antix, AoscOS, AoscOS_old, AoscOsRetro, AoscOsRetro_small, Aperture, Apple, AppleSmall, Apricity, Arch, Arch2, ArchBox, Archcraft, Archcraft2, Archlabs, ArchSmall, ArchStrike, ArcoLinux, ArcoLinuxSmall, ArseLinux, Artix, Artix2Small, ArtixSmall, Arya, Asahi, Asahi2, Aster, AsteroidOS, AstOS, Astra, Ataraxia, Athena, Azos
B: Bedrock, BigLinux, Bitrig, BlackArch, BlackMesa, BlackPanther, BLAG, BlankOn, BlueLight, Bodhi, Bonsai, BSD, BunsenLabs
C: CachyOS, CachyOSSmall, Calculate, CalinixOS, CalinixOSSmall, Carbs, CBL-Mariner, CelOS, Center, CentOS, CentOSSmall, Chakra, ChaletOS, Chapeau, Chimera Linux, ChonkySealOS, Chrom, Cleanjaro, CleanjaroSmall, ClearLinux, ClearOS, Clover, Cobalt, Condres, ContainerLinux, CRUX, CRUXSmall, CrystalLinux, Cucumber, CutefishOS, CuteOS, CyberOS, cycledream
D: Dahlia, DarkOS, Debian, DebianSmall, Deepin, DesaOS, Devuan, DevuanSmall, DietPi, DracOS, DragonFly, DragonFlyOld, DragonFlySmall, DraugerOS, Droidian
E: Elbrus, Elementary, ElementarySmall, Elive, EncryptOS, Endeavour, EndeavourSmall, Endless, Enso, EshanizedOS, EuroLinux, evolinx, EvolutionOS, EvolutionOS_old, EvolutionOSSmall, Exherbo, ExodiaPredator
F: Fedora, FedoraAsahiRemix, FedoraCoreOS, FedoraKinoite, FedoraOld, FedoraOnyx placeholder - Currently does not have a logo, FedoraSericea, FedoraSilverblue, FedoraSmall, FemboyOS, Feren, Finnix, Floflis, FreeBSD, FreeBSDSmall, FreeMiNT, Frugalware, Funtoo
G: GalliumOS, Garuda, GarudaDragon, GarudaSmall, Gentoo, GentooSmall, GhostBSD, Glaucus, GNewSense, Gnome, GNU, GoboLinux, GrapheneOS, Grombyang, Guix, GuixSmall
H: Haiku, HaikuSmall, HamoniKR, HarDClanZ, HardenedBSD, Hash, Huayra, Hybrid, HydroOS, Hyperbola, HyperbolaSmall
I: Iglunix, InstantOS, Interix, IRIX, Ironclad, Itc
J: Januslinux
K: Kaisen, Kali, KaliSmall, KaOS, KDENeon, KernelOS, Kibojoe, KISSLinux, Kogaion, Korora, KrassOS, KSLinux, Kubuntu
L: LainOS, LangitKetujuh, Laxeros, LEDE, LibreELEC, Linspire, Linux, LinuxLight, LinuxLightSmall, LinuxMint, LinuxMintOld, LinuxMintSmall, LinuxSmall, Live_Raizo, LMDE, Lunar
M: MacOS, MacOS2, MacOS2Small, MacOSSmall, Mageia, MageiaSmall, MagpieOS, MainsailOS, MainsailOSSmall, Mandriva, Manjaro, ManjaroSmall, MassOS, MatuusOS, MaUI, Meowix, Mer, Minix, Mint, MintOld, MintSmall, MiracleLinux, MOS, Msys2, MX, MX2, MXSmall
N: Namib, Nekos, Neptune, NetBSD, NetRunner, Nitrux, NixOS, NixOSOld, NixOsOldSmall, NixOSSmall, Nobara, NomadBSD, Nurunner, NuTyX
O: Obarun, OBRevenge, OmniOS, OpenBSD, OpenBSDSmall, OpenEuler, OpenIndiana, OpenKylin, OpenMamba, OpenMandriva, OpenStage, OpenSuse, OpenSuseLeap, openSuseMicroOS, OpenSuseSmall, OpenSuseTumbleweed, OpenWrt, OPNsense, Oracle, Orchid, OrchidSmall, OS_Elbrus, OSMC, OSX, OSXSmall
P: PacBSD, Panwah, Parabola, ParabolaSmall, Parch, Pardus, Parrot, Parsix, PCBSD, PCLinuxOS, PearOS, Pengwin, Pentoo, Peppermint, Peropesis, PhyOS, PikaOS, Pisi, PNMLinux, Pop, PopSmall, Porteus, PostMarketOS, PostMarketOSSmall, Proxmox, PuffOS, Puppy, PureOS, PureOSSmall
Q: Q4OS, Qubes, Qubyt, Quibian
R: Radix, Raspbian, RaspbianSmall, RavynOS, Reborn, RebornSmall, RedCore, RedHatEnterpriseLinux, RedHatEnterpriseLinux_old, RedstarOS, Refracted Devuan, Regata, Regolith, RhaymOS, RockyLinux, RockyLinuxSmall, RosaLinux
S: Sabayon, Sabotage, Sailfish, SalentOS, SalientOS, Salix, SambaBOX, Sasanqua, Scientific, Semc, Septor, Serene, SharkLinux, ShastraOS, Siduction, SkiffOS, Slackel, Slackware, SlackwareSmall, Slitaz, SmartOS, Soda, Solaris, SolarisSmall, Solus, Source Mage, Sparky, Star, SteamOS, StockLinux, Sulin, Suse, SuseSmall, Swagarch
T: T2, Tails, Tatra, TeArch, TileOS, TorizonCore, Trisquel, TuxedoOS, Twister
U: Ubuntu, Ubuntu2Old, Ubuntu2Small, UbuntuBudgie, UbuntuCinnamon, UbuntuGnome, UbuntuKde, UbuntuKylin, UbuntuMate, UbuntuOld, UbuntuSmall, UbuntuStudio, UbuntuSway, UbuntuTouch, UbuntuUnity, Ultramarine, Univalent, Univention, UOS, UrukOS, Uwuntu
V: Vanilla, Venom, VenomSmall, Vnux, Void, VoidSmall, Vzlinux
W: WiiLinuxNgx, Windows, Windows11, Windows11Small, Windows8, Windows95
X: Xenia, Xferience
Y: YiffOS
Z: Z/OS, Zorin
```

Run `fastfetch --print-logos` to print them

### Package managers
```
am, apk, brew, Chocolatey, dpkg, emerge, eopkg, Flatpak, MacPorts, nix, opkg, Pacman, paludis, pkg, pkgtool, rpm, scoop, Snap, sorcery, winget (disabled by default), xbps
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
