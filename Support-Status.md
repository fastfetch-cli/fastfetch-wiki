All categories not listed here should work without needing a specific implementation.

### Available Modules
<!-- fastfetch --list-modules -->
```
1)  Battery       : Print battery capacity, status, etc
2)  BIOS          : Print information of 1st-stage bootloader (name, version, release date, etc)
3)  Bluetooth     : List (connected) bluetooth devices
4)  BluetoothRadio: List bluetooth radios width supported version and vendor
5)  Board         : Print motherboard name and other info
6)  Bootmgr       : Print information of 2nd-stage bootloader (name, firmware, etc)
7)  Break         : Print a empty line
8)  Brightness    : Print current brightness level of your monitors
9)  Btrfs         : Print Linux BTRFS volumes
10) Camera        : Print available cameras
11) Chassis       : Print chassis type (desktop, laptop, etc)
12) Command       : Run custom shell scripts
13) Colors        : Print some colored blocks
14) CPU           : Print CPU name, frequency, etc
15) CPUCache      : Print CPU cache sizes
16) CPUUsage      : Print CPU usage. Costs some time to collect data
17) Cursor        : Print cursor style name
18) Custom        : Print a custom string, with or without key
19) DateTime      : Print current date and time
20) DE            : Print desktop environment name
21) Display       : Print resolutions, refresh rates, etc
22) Disk          : Print partitions, space usage, file system, etc
23) DiskIO        : Print physical disk I/O throughput
24) DNS           : Print configured DNS servers
25) Editor        : Print information of the default editor ($VISUAL or $EDITOR)
26) Font          : Print system font names
27) Gamepad       : List (connected) gamepads
28) GPU           : Print GPU names, graphic memory size, type, etc
29) Host          : Print product name of your computer
30) Icons         : Print icon style name
31) InitSystem    : Print init system (pid 1) name and version
32) Kernel        : Print system kernel version
33) Keyboard      : List (connected) keyboards
34) LM            : Print login manager (desktop manager) name and version
35) Loadavg       : Print system load averages
36) Locale        : Print system locale name
37) LocalIp       : List local IP addresses (v4 or v6), MAC addresses, etc
38) Logo          : Query built-in logo for JSON output
39) Media         : Print playing song name
40) Memory        : Print system memory usage info
41) Monitor       : Alias of Display module
42) Mouse         : List connected mouses
43) NetIO         : Print network I/O throughput
44) OpenCL        : Print highest OpenCL version supported by the GPU
45) OpenGL        : Print highest OpenGL version supported by the GPU
46) OS            : Print operating system name and version
47) Packages      : List installed package managers and count of installed packages
48) PhysicalDisk  : Print physical disk information
49) PhysicalMemory: Print system physical memory devices
50) Player        : Print music player name
51) PowerAdapter  : Print power adapter name and charging watts
52) Processes     : Print number of running processes
53) PublicIp      : Print your public IP address, etc
54) Separator     : Print a separator line
55) Shell         : Print current shell name and version
56) Sound         : Print sound devices, volume, etc
57) Swap          : Print swap (paging file) space usage
58) Terminal      : Print current terminal name and version
59) TerminalFont  : Print font name and size used by current terminal
60) TerminalSize  : Print current terminal size
61) TerminalTheme : Print current terminal theme (foreground and background colors)
62) Title         : Print title, which contains your user name, hostname
63) Theme         : Print current theme of desktop environment
64) TPM           : Print info of Trusted Platform Module (TPM) Security Device
65) Uptime        : Print how long system has been running
66) Users         : Print users currently logged in
67) Version       : Print Fastfetch version
68) Vulkan        : Print highest Vulkan version supported by the GPU
69) Wallpaper     : Print image file path of current wallpaper
70) Weather       : Print weather information
71) WM            : Print window manager name and version
72) Wifi          : Print connected Wi-Fi info (SSID, connection and security protocol)
73) WMTheme       : Print current theme of window manager
74) Zpool         : Print ZFS storage pools
```

Worth noting that not all modules are supported on all platforms.

### Builtin logos
<!-- a = `Content of src/logo/builtin.c`; Object.entries(Object.groupBy(a.split('\n').map(x => x.trim()).filter(x => x.startsWith('// ')).map(x => x.slice(3)).filter(x => x != 'LAST').sort((a, b)=>a.toUpperCase().localeCompare(b.toUpperCase())), x => x[0].toUpperCase())).map(([key, arr]) => `${key}: ${arr.join(', ')}`).join('\n') -->
```
A: Adélie, Aeon, AerOS, AerynOS, Afterglow, AIX, AlmaLinux, Alpine, Alpine2, Alpine2Small, Alpine3Small, AlpineSmall, Alter, ALTLinux, Amazon, AmazonLinux, Amiga, AmogOS, Anarchy, Android, AndroidSmall, AnduinOS, Antergos, Antix, AnushOS, AoscOS, AoscOS_old, AoscOsRetro, AoscOsRetro_small, Aperture, Apple, AppleSmall, Apricity, Arch, Arch2, Arch3, ArchBox, Archcraft, Archcraft2, Archlabs, ArchOld, ArchSmall, ArchStrike, ArcoLinux, ArcoLinuxSmall, Arkane, Armbian, Armbian2, ArseLinux, Artix, Artix2Small, ArtixSmall, Arya, Asahi, Asahi2, Aster, AsteroidOS, AstOS, Astra, Ataraxia, AthenaOS, AthenaOS_old, Aurora, AxOS, Azos
B: Bedrock, BedrockSmall, BigLinux, Bitrig, BlackArch, BlackMesa, BlackPanther, BLAG, BlankOn, BlueLight, Bodhi, Bonsai, BredOS, BSD, BunsenLabs
C: CachyOS, CachyOSSmall, Calculate, CalinixOS, CalinixOSSmall, Carbs, CBL-Mariner, CelOS, Center, CentOS, CentOSSmall, Cereus, Chakra, ChaletOS, Chapeau, Chimera, ChonkySealOS, Chrom, Cleanjaro, CleanjaroSmall, ClearLinux, ClearOS, Clover, Cobalt, Codex Linux (reMarkable OS), Common Torizon, Condres, ContainerLinux, Cosmic DE, CRUX, CRUXSmall, CrystalLinux, Cucumber, CuerdOS, CutefishOS, CuteOS, CyberOS, cycledream
D: DahliaOS, DarkOS, Debian, DebianSmall, deepin, DesaOS, Devuan, DevuanSmall, DietPi, DracOS, DragonFly, DragonFlyOld, DragonFlySmall, DraugerOS, Droidian
E: Elbrus, Elementary, ElementarySmall, Elive, EmperorOS, EncryptOS, EndeavourOS, EndeavourOSSmall, Endless, Enso, EshanizedOS, EuroLinux, EvolutionOS, EvolutionOS_old, EvolutionOSSmall, eweOS, Exherbo, ExodiaPredator
F: Fastfetch, Fedora, FedoraAsahiRemix, FedoraCoreOS, FedoraKinoite, FedoraOld, FedoraSericea, FedoraSilverblue, FedoraSmall, FemboyOS, Feren, Filotimo, Finnix, Floflis, FreeBSD, FreeBSDSmall, FreeMiNT, Frugalware, Funtoo, Furreto
G: GalliumOS, Garuda, GarudaDragon, GarudaSmall, Gentoo, GentooSmall, GhostBSD, GhostFreak, Glaucus, GNewSense, GNOME, GNU, GoboLinux, GoldenDogLinux, GrapheneOS, Grombyang, Guix, GuixSmall, GXDE
H: Haiku, Haiku2, HaikuSmall, HamoniKR, HarDClanZ, HardenedBSD, HarmonyOS, Hash, HeliumOS, Huawei Cloud EulerOS, Huayra, Hybrid, HydroOS, Hyperbola, HyperbolaSmall, HyprOS
I: Iglunix, InstantOS, Interix, IRIX, Ironclad, Itc
J: Januslinux
K: Kaisen, Kali, KaliSmall, Kalpa Desktop, KaOS, KDE Neon, KDELinux, KernelOS, Kibojoe, KISSLinux, Kogaion, Korora, KrassOS, KSLinux, Kubuntu, Kylin
L: LainOS, LangitKetujuh, Laxeros, LEDE, LibreELEC, Lilidog, Lingmo OS, Linspire, Linux, LinuxFromScratch, LinuxLight, LinuxLightSmall, LinuxMint, LinuxMintOld, LinuxMintSmall, LinuxSmall, Live_Raizo, LliureX, LMDE, Loc-OS, Lubuntu, Lunar
M: Macaroni, MacOS, MacOS2, MacOS2Small, MacOS3, MacOSSmall, Mageia, MageiaSmall, Magix, MagpieOS, MainsailOS, MainsailOSSmall, Mandriva, Manjaro, ManjaroSmall, MassOS, MatuusOS, MaUI, Mauna, Meowix, Mer, MidnightBSD, MidOS, MidOSOld, Minimal System, Minix, MiracleLinux, MOS, Msys2, MX, MX2, MXSmall
N: Namib, Nekos, Neptune, NetBSD, NetBSD Small, NetBSD2, NetHydra, NetRunner, NexaLinux, Nitrux, NixOS, NixOSOld, NixOsOldSmall, NixOSSmall, Nobara, NomadBSD, NurOS, Nurunner, NuTyX
O: Obarun, OBRevenge, ObsidianOS, OmniOS, OpenBSD, OpenBSDSmall, OpenEuler, OpenIndiana, OpenKylin, OpenMamba, OpenMandriva, OpenStage, OpenSuse, OpenSuseLeap, OpenSuseLeapOld, openSuseMicroOS, openSUSESlowroll, OpenSuseSmall, OpenSuseTumbleweed, OpenSuseTumbleweed2, OpenSuseTumbleweedOld, OpenSuseTumbleweedSmall, OpenWrt, OPNsense, Oracle, Orchid, OrchidSmall, Oreon, OS_Elbrus, OS/2 Warp, OSMC, OSX, OSXSmall
P: PacBSD, Panwah, Parabola, ParabolaSmall, Parch, Pardus, Parrot, Parsix, PCBSD, PCLinuxOS, PearOS, Pengwin, Pentoo, Peppermint, Peropesis, PhyOS, PikaOS, PisiLinux, PNMLinux, Pop, PopSmall, Porteus, PostMarketOS, PostMarketOSSmall, PrismLinux, PrismLinuxSmall, Proxmox, PuffOS, Puppy, PureOS, PureOSSmall
Q: Q4OS, QTS, Qubes, Qubyt, Quibian, Quirinux
R: Radix, Raspbian, RaspbianSmall, RavynOS, RebornOS, RebornSmall, RedCore, RedHatEnterpriseLinux, RedHatEnterpriseLinux, RedHatEnterpriseLinux_old, RedOS, RedOS small, RedstarOS, Refracted Devuan, Regata, Regolith, RhaymOS, RhinoLinux, RockyLinux, RockyLinuxSmall, RosaLinux
S: Sabayon, Sabotage, Sailfish, SalentOS, SalientOS, Salix, SambaBOX, Sasanqua, Scientific, Secureblue, Semc, Septor, Serene, Serpent OS, SharkLinux, ShastraOS, Shebang, Siduction, SkiffOS, Slackel, Slackware, SlackwareSmall, SleeperOS, SleeperOS, Slitaz, SmartOS, SnigdhaOS, Soda, Solaris, SolarisSmall, Solus, Source Mage, Sparky, SpoinkOS, Star, Steam Deck, Steam Deck OLED, Steam Deck Small, SteamOS, StockLinux, Sulin, SummitOS, Suse, SuseSmall, Swagarch
T: T2, T2Small, Tails, Tatra, TeArch, TempleOS, TileOS, Torizon OS, Trisquel, TrueNAS Scale, TuxedoOS, Twister
U: UBLinux, UBLinuxSmall, Ubuntu, UbuntuBudgie, UbuntuCinnamon, UbuntuGNOME, UbuntuKde, UbuntuKylin, UbuntuMate, UbuntuOld, UbuntuOld2, UbuntuOld2Small, UbuntuSmall, UbuntuStudio, UbuntuSway, UbuntuTouch, UbuntuUnity, Ultramarine, Ultramarine Small, Unifi, Univalent, Univention, UOS, UrukOS, Uwuntu
V: Valhalla, Vanilla, Vanilla2, VanillaSmall, Venom, VenomSmall, VincentOS, Vnux, Void, Void2, Void2Small, VoidSmall, Vzlinux
W: WiiLinux, Windows, Windows11, Windows11Small, Windows2025, Windows8, Windows95, WolfOS
X: XCP-ng, Xenia, Xenia_old, Xferience, Xinux, Xubuntu
Y: YiffOS
Z: Z/OS, Zorin, Zraxyl
```

Run `fastfetch --print-logos` to print them

### Package managers
```
am, apk, brew, Chocolatey, dpkg, emerge, eopkg, Flatpak, Guix, hpkg, linglong, lpkg, lpkgbuild, MacPorts, mport, nix, opkg, Pacman, pacstall, paludis, pisi, pkg, pkgtool, pkgsrc, qi, rpm, scoop, Snap, soar, sorcery, winget (disabled by default), xbps
```

### WM themes
```
DWM (Windows), KWin, Marco, Muffin, Mutter, Openbox (LXDE, LXQT & without DE), Quartz Compositor (macOS), XFWM
```

### DE versions
```
Budgie, Cinnamon, COSMIC, Gnome, KDE Plasma, LXQt, Mate, Unity, XFCE4
```

### Terminal fonts
```
Alacritty, Apple Terminal, ConEmu, cosmic-term, Deepin Terminal, foot, Gnome Console, Gnome Terminal, iTerm2, Kitty, Konsole, LXTerminal, MATE Terminal, mintty, QTerminal, Rio, st, Tabby, Terminator, Termux, Tilix, TTY, urxvt, Warp, WezTerm, Windows Terminal, XFCE4 Terminal, xterm, Yakuake
```
