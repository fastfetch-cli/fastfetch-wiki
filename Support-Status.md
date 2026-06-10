All categories not listed here should work without needing a specific implementation.

### Available Modules
<!-- fastfetch --list-modules -->
```
1)  Battery       : Print battery information
2)  BIOS          : Print first-stage bootloader information (name, version, release date, etc.)
3)  Bluetooth     : List connected Bluetooth devices
4)  BluetoothRadio: List Bluetooth radios (supported versions, vendors, etc.)
5)  Board         : Print motherboard name and other information
6)  Bootmgr       : Print second-stage bootloader information (name, firmware, etc.)
7)  Break         : Print an empty line
8)  Brightness    : Print the current brightness level of your monitors
9)  Btrfs         : Print Linux BTRFS volumes
10) Camera        : Print available cameras
11) Chassis       : Print chassis type information (desktop, laptop, etc.)
12) Codec         : Print hardware video acceleration codec types (decode / encode)
13) Command       : Run custom shell scripts
14) Colors        : Display the terminal's 16-color palette
15) CPU           : Print CPU name, frequency, etc.
16) CPUCache      : Print CPU cache sizes
17) CPUUsage      : Print CPU usage. Collecting data takes some time
18) Cursor        : Print cursor style name
19) Custom        : Print a custom string, with or without key
20) DateTime      : Print the current date and time
21) DE            : Print desktop environment name
22) Display       : Print resolutions, refresh rates, etc
23) Disk          : Print partitions, space usage, file system, etc
24) DiskIO        : Print physical disk I/O throughput
25) DNS           : Print configured DNS servers
26) Editor        : Print information about the default editor ($VISUAL or $EDITOR)
27) Font          : Print system font names
28) Gamepad       : List connected gamepads
29) GPU           : Print GPU names, memory sizes, types, etc
30) Host          : Print your computer's product name
31) Icons         : Print icon style name
32) InitSystem    : Print init system (pid 1) name and version
33) Kernel        : Print system kernel version
34) Keyboard      : List connected keyboards
35) LM            : Print login manager (desktop manager) name and version
36) Loadavg       : Print system load averages
37) Locale        : Print system locale name
38) LocalIp       : List local IP addresses (IPv4 or IPv6), MAC addresses, etc
39) Logo          : Query built-in logo for JSON output
40) Media         : Print the name of the currently playing song
41) Memory        : Print system memory usage information
42) Monitor       : Same as Display module, but with a different default output format
43) Mouse         : List connected mice
44) NetIO         : Print network I/O throughput
45) OpenCL        : Print the highest OpenCL version supported by the GPU
46) OpenGL        : Print the highest OpenGL version supported by the GPU
47) OS            : Print the OS or Linux distribution name and version
48) Packages      : List installed package managers and count of installed packages
49) PhysicalDisk  : Print physical disk information
50) PhysicalMemory: Print system physical memory devices
51) Player        : Print the music player name that is currently active
52) PowerAdapter  : Print power adapter name and charging watts
53) Processes     : Print number of running processes
54) PublicIp      : Print your public IP address and related information
55) Separator     : Print a separator line
56) Shell         : Print the current shell name and version
57) Sound         : Print sound devices, volume levels, etc
58) Swap          : Print swap (paging file) space usage
59) Terminal      : Print the current terminal name and version
60) TerminalFont  : Print the font name and size used by the current terminal
61) TerminalSize  : Print the current terminal size
62) TerminalTheme : Print the current terminal theme (foreground and background colors)
63) Title         : Print the title, including your username and hostname
64) Theme         : Print the current desktop environment theme
65) TPM           : Print information about the Trusted Platform Module (TPM) security device
66) Uptime        : Print how long the system has been running
67) Users         : Print users who are currently logged in
68) Version       : Print the Fastfetch version and build information
69) Vulkan        : Print the highest Vulkan version supported by the GPU
70) Wallpaper     : Print the file path of the current wallpaper
71) Weather       : Print weather information
72) WM            : Print the window manager name and version
73) Wifi          : Print connected Wi-Fi info (SSID, connection and security protocol)
74) WMTheme       : Print the current window manager theme
75) Zpool         : Print ZFS storage pools
```

Worth noting that not all modules are supported on all platforms.

### Builtin logos
<!-- a = `grep -h '^\s*// .*' src/logo/ascii/*.inc | pbcopy`; Object.entries(Object.groupBy(a.split('\n').map(x => x.trim().slice(3)).filter(x => x && x != 'LAST').sort((a, b) => a.toUpperCase().localeCompare(b.toUpperCase())), x => x[0].toUpperCase())).map(([key, arr]) => `${key}: ${arr.join(', ')}`).join('\n') -->

```
A: Adélie, Aeon, AerOS, AerynOS, AerynOS_old, Afterglow, AIX, AlmaLinux, Alpine, Alpine2, Alpine2Small, Alpine3Small, AlpineSmall, Alter, ALTLinux, Amazon, AmazonLinux, Amiga, AmogOS, Anarchy, Android, AndroidSmall, AnduinOS, Antergos, Antix, AnushOS, AoscOS, AoscOS_old, AoscOsRetro, AoscOsRetro_small, Aperture, Apple, AppleSmall, Apricity, Arch, Arch2, Arch3, ArchBox, Archcraft, Archcraft2, Archlabs, ArchOld, ArchSmall, ArchStrike, ArcoLinux (Discontinued), ArcoLinuxSmall, Arkane, Armbian, Armbian2, ArseLinux, Artix, Artix2Small, ArtixSmall, Arya, Asahi, Asahi2, Aster, AsteroidOS, AstOS, Astra, Ataraxia, AthenaOS, AthenaOS_old, Aurora, AxOS, Azos
B: Bedrock, BedrockSmall, BerserkArch, BigLinux, Bitrig, BlackArch, BlackMesa, BlackPanther, BLAG, BlankOn, BlueLight, Bodhi, Bonsai, BredOS, BSD, BunsenLabs
C: CachyOS, CachyOSSmall, Calculate, CalinixOS, CalinixOSSmall, Carbs, CBL-Mariner, CelOS, Center, CentOS, CentOSSmall, Cereus, Chakra, ChaletOS, Chapeau, Chimera, ChonkySealOS, Chrom, Cleanjaro, CleanjaroSmall, ClearLinux, ClearOS, Clover, Cobalt, Codex Linux (reMarkable OS), Common Torizon, Condres, ContainerLinux, Cosmic DE, CRUX, CRUXSmall, CrystalLinux, Cucumber, CuerdOS, CutefishOS, CuteOS, CyberOS, cycledream
D: DahliaOS, DarkOS, Debian, DebianSmall, Deepin, DesaOS, Devuan, DevuanSmall, DietPi, DracOS, DragonFly, DragonFlyOld, DragonFlySmall, DraugerOS, Droidian
E: Elbrus, Elementary, ElementarySmall, Elive, Emmabuntüs, EmperorOS, EN-OS, EncryptOS, EndeavourOS, EndeavourOSSmall, Endless, Enso, EshanizedOS, EuroLinux, EvolutionOS, EvolutionOS_old, EvolutionOSSmall, eweOS, Exherbo, ExodiaOS
F: Fastfetch, Fedora, FedoraAsahiRemix, FedoraCoreOS, FedoraKinoite, FedoraOld, FedoraSericea, FedoraSilverblue, FedoraSmall, FemboyOS, Feren, Filotimo, Finnix, Floflis, FreeBSD, FreeBSDSmall, FreeMiNT, Frugalware, Funtoo, Furreto
G: GalliumOS, Garuda, GarudaDragon, GarudaSmall, Gentoo, GentooSmall, GhostBSD, GhostFreak, Glaucus, GNewSense, GNOME OS, GNU, GoboLinux, GoldenDogLinux, GrapheneOS, Grombyang, Guix, GuixSmall, GXDE
H: Haiku, Haiku2, HaikuSmall, HamoniKR, HarDClanZ, HardenedBSD, HarmonyOS, Hash, HeliumOS, Huawei Cloud EulerOS, Huayra, Hybrid, HydroOS, Hyperbola, HyperbolaSmall, HyprOS
I: Iglunix, InstantOS, Interix, IRIX, Ironclad, Itc
J: Januslinux
K: Kaisen, Kali, KaliSmall, Kalpa Desktop, KaOS, KDE Neon, KDELinux, KernelOS, KibaOS, Kibojoe, KISSLinux, KISSLinux2, Kogaion, Korora, KrassOS, KSLinux, Kubuntu, Kylin
L: LainOS, LangitKetujuh, Laxeros, LEDE, LibreELEC, Lilidog, LimeOS, Lingmo OS, Linspire, Linux, LinuxFromScratch, LinuxLite, LinuxLiteSmall, LinuxMint, LinuxMint2, LinuxMintOld, LinuxMintSmall, LinuxSmall, Live_Raizo, LliureX, LMDE, Loc-OS, Lubuntu, Lunar
M: Macaroni, MacOS, MacOS2, MacOS2Small, MacOS3, MacOSSmall, Mageia, MageiaSmall, Magix, MagpieOS, MainsailOS, MainsailOSSmall, Mandriva, Manjaro, ManjaroSmall, MassOS, MatuusOS, MaUI, Mauna, Meowix, Mer, MidnightBSD, MidOS, MidOSOld, Minimal System, Minix, MiracleLinux, MOS, Msys2, MX, MX2, MXSmall
N: Namib, NebiOS, Nekos, Neptune, NetBSD, NetBSD Small, NetBSD2, NetHydra, NetRunner, NexaLinux, Nitrux, NixOS, NixOS2, NixOSOld, NixOsOldSmall, NixOSSmall, Nobara, NomadBSD, NurOS, Nurunner, NuTyX
O: Obarun, OBRevenge, ObsidianOS, OmniOS, OpenBSD, OpenBSDSmall, OpenEuler, OpenIndiana, OpenKylin, OpenMamba, OpenMandriva, openRuyi, OpenStage, OpenSuse, OpenSuseLeap, OpenSuseLeapOld, openSuseMicroOS, openSUSESlowroll, OpenSuseSmall, OpenSuseTumbleweed, OpenSuseTumbleweed2, OpenSuseTumbleweedOld, OpenSuseTumbleweedSmall, OpenWrt, OpenWrtOld, OpenWrtSmall, OPNsense, Oracle, Orchid, OrchidSmall, Oreon, Origami, OrigamiSmall, OS_Elbrus, OS/2 Warp, OSMC, OSX, OSXSmall
P: PacBSD, Panwah, Parabola, ParabolaSmall, Parch, Pardus, Parrot, Parsix, PCBSD, PCLinuxOS, PearOS, Pengwin, Pentoo, Peppermint, Peropesis, PhyOS, PikaOS, PisiLinux, PNMLinux, Pop, PopSmall, Porteus, PostMarketOS, PostMarketOSSmall, PrismLinux, PrismLinuxSmall, Proxmox, PuffOS, Puppy, PureOS, PureOSSmall
Q: Q4OS, QTS, Quasar, Qubes, Qubyt, Quibian, Quirinux
R: Radix, Raspbian, RaspbianSmall, RavynOS, RebornOS, RebornSmall, RedCore, RedHatEnterpriseLinux, RedHatEnterpriseLinux, RedHatEnterpriseLinux_old, RedOS, RedOS small, Redrose, RedstarOS, Refracta, Regata, Regolith, RhaymOS, RhinoLinux, RockyLinux, RockyLinuxSmall, RosaLinux
S: Sabayon, Sabotage, Sailfish, SalentOS, SalientOS, Salix, SambaBOX, Sasanqua, Scientific, Secureblue, Semc, Septor, Serene, Serpent OS, SharkLinux, ShastraOS, Shebang, Siduction, SkiffOS, Slackel, Slackware, SlackwareSmall, SleeperOS, SleeperOSSmall, Slitaz, SmartOS, SnigdhaOS, Soda, Solaris, SolarisSmall, Solus, Source Mage, Sparky, SpoinkOS, Star, Steam Deck, Steam Deck OLED, Steam Deck Small, SteamOS, StockLinux, Sulin, SummitOS, Suse, SuseSmall, Swagarch
T: T2, T2Small, Tails, Tatra, TeArch, TempleOS, TileOS, Torizon OS, Trisquel, TrueNAS Scale, TuxedoOS, Twister
U: UBLinux, UBLinuxSmall, Ubuntu, UbuntuBudgie, UbuntuCinnamon, UbuntuGNOME, UbuntuKylin, UbuntuMate, UbuntuOld, UbuntuOld2, UbuntuOld2Small, UbuntuSmall, UbuntuStudio, UbuntuSway, UbuntuTouch, UbuntuUnity, Ultramarine, Ultramarine Small, Unifi, Univalent, Univention, UOS, UrukOS, Uwuntu, Uzbek
V: Valhalla, Vanilla, Vanilla2, VanillaSmall, Venom, VenomSmall, VincentOS, Vnux, Void, Void2, Void2Small, VoidSmall, Vzlinux
W: WiiLinux, Windows, Windows11, Windows11Small, Windows2025, Windows8, Windows95, WolfOS
X: XCP-ng, Xenia, Xenia_old, XeroArch, Xferience, Ximper, Xinux, XJ380, Xray_OS, Xubuntu
Y: YiffOS
Z: Z/OS, Zorin, Zraxyl
```

Run `fastfetch --print-logos` to print them