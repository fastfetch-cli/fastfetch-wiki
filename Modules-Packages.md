# Packages

> List installed package managers and count of installed packages

| | |
|---|---|
| Module type | `packages` |
| Default order | 14 (only used by `--gen-config`) |
| Module source | `src/modules/packages/packages.c` |
| Detection source | `src/detection/packages/` |

Prints one `count (manager)` pair for every package manager that was found to have at least one
package installed, in a fixed order defined by the source.

```
Packages: 414 (brew), 22 (brew-cask)
```

```
Packages: 1234 (dpkg), 42 (flatpak), 87 (snap)
```

```
Packages: 51 (pacman)[stable], 3 (appimage)
```

The `[stable]` suffix appears only for pacman, and only on distributions that publish a pacman
branch (Manjaro and friends). A manager that is installed but has zero packages is omitted
entirely, and the total is never shown in the text output — only in JSON, as `all`.

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `packages_linux.c`, `packages_nix.c` | Roughly 30 managers, each from its own database file or directory |
| Android | `packages_linux.c`, `packages_nix.c` | Same readers; most of the paths will not exist |
| FreeBSD | `packages_bsd.c` | `pkg` and `mport`, both from SQLite databases |
| NetBSD | `packages_nbsd.c` | `pkgsrc` only |
| OpenBSD | `packages_obsd.c` | `pkg` only |
| Solaris / illumos | `packages_sunos.c` | `pkg` from `catalog.attrs`, `pkgsrc` from a directory |
| Haiku | `packages_haiku.c` | `hpkg` system and user package directories |
| GNU/Hurd | `packages_linux.c`, `packages_nix.c` | Same readers as Linux |
| macOS | `packages_apple.c`, `packages_nix.c` | `brew`, `macports`, `nix` |
| Windows | `packages_windows.c` | `scoop`, `choco`, `pacman` (MSYS2), `winget` |

There is no `packages_nosupport.c`: the module has a real implementation for every platform, so
the detection directory contains only readers that are actually compiled into some build.

`packages_nix.c` is compiled into the Linux, Android, GNU/Hurd and macOS blocks only; it is what
implements `nix` on those platforms and is also used by macOS for `nix-default`, `nix-system` and
`nix-user`.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `disabled` | array of strings, or `null` | build-time value (see below) | Package managers to skip during detection. `null` disables nothing. |
| `combined` | boolean | `false` | Merge related managers into a single count (see below). |
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works, `""` prints none. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

### `disabled`

Values are matched case-insensitively, so `"brew"` and `"Brew"` both work. Accepted names:

`am`, `apk`, `appimage`, `brew`, `cards`, `choco`, `crux`, `dpkg`, `emerge`, `eopkg`, `flatpak`,
`guix`, `hpkg`, `installrelease`, `kiss`, `linglong`, `lpkg`, `lpkgbuild`, `macports`, `moss`,
`mport`, `nix`, `opkg`, `pacman`, `pacstall`, `paludis`, `pisi`, `pkg`, `pkgsrc`, `pkgtool`,
`porg`, `rpm`, `rum`, `scoop`, `snap`, `soar`, `sorcery`, `winget`, `xbps`

The schema's `enum` for this key lists all 39 names, in the same order, so a schema-validating
editor accepts exactly what the runtime accepts. The two lists are maintained by hand — one in the
parser's `switch`, one in `doc/json_schema.json` — and they have drifted before, so if your editor
rejects a name that fastfetch accepts, the list above is the one to trust.

The **default is a build-time value, not an empty list**, which is why the schema declares no
`default` for this key. `CMakeLists.txt` parses `src/modules/packages/option.h` to derive one
`-DPACKAGES_DISABLE_<NAME>=ON` option per manager and turns the enabled ones into the
`FF_PACKAGES_DISABLE_LIST` macro, which becomes the initial value of `disabled`. A distribution
that builds with `-DPACKAGES_DISABLE_NIX=ON` ships a binary where `nix` is off until the user
removes it from `disabled` explicitly. With `-DPACKAGES_REMOVE_DISABLED=ON` the runtime `disabled`
key disappears altogether and the skip list is frozen into the build.

```jsonc
{ "type": "packages", "disabled": ["nix", "flatpak", "snap"] }
```

### `combined`

Merges a manager's sub-counters into one number and prints the group's short name:

| Group | Members | Printed as |
|---|---|---|
| `brew` | `brew`, `brew-cask` | `<total> (brew)` |
| `flatpak` | `flatpak-system`, `flatpak-user` | `<total> (flatpak)` |
| `nix` | `nix-default`, `nix-system`, `nix-user` | `<total> (nix)` |
| `guix` | `guix-system`, `guix-user`, `guix-home` | `<total> (guix)` |
| `hpkg` | `hpkg-system`, `hpkg-user` | `<total> (hpkg)` |
| `scoop` | `scoop-user`, `scoop-global` | `<total> (scoop)` |
| `am` | `am-system`, `am-user` | `<total> (am)` |

`combined` affects **only the text output**. The JSON result always uses the per-manager keys,
whatever `combined` says.

Even without `combined`, three groups collapse on their own when only one side has packages:
`flatpak` prints `N (flatpak)` when there are no user packages, `hpkg` prints `N (hpkg)` when
there are no user packages, and `scoop` prints `N (scoop)` when there are no global packages.
`brew`, `nix`, `guix` and `am` do not do this — they always print both sub-names separately. For
`am` the two sub-names are `am` (system) and `appman` (user), matching the format variables
`{am-system}` and `{am-user}`.

## Format string

Run `fastfetch -h packages-format` for the authoritative list. There are 55 variables:

| Group | Variables |
|---|---|
| Per-manager counts | `{am-system}`, `{am-user}`, `{appimage}`, `{apk}`, `{brew}`, `{brew-cask}`, `{cards}`, `{choco}`, `{crux}`, `{dpkg}`, `{emerge}`, `{eopkg}`, `{flatpak-system}`, `{flatpak-user}`, `{guix-home}`, `{guix-system}`, `{guix-user}`, `{hpkg-system}`, `{hpkg-user}`, `{install-release}`, `{kiss}`, `{linglong}`, `{lpkg}`, `{lpkgbuild}`, `{macports}`, `{mport}`, `{moss}`, `{nix-default}`, `{nix-system}`, `{nix-user}`, `{opkg}`, `{pacman}`, `{pacstall}`, `{paludis}`, `{pisi}`, `{pkg}`, `{pkgsrc}`, `{pkgtool}`, `{porg}`, `{rpm}`, `{rum}`, `{scoop-global}`, `{scoop-user}`, `{snap}`, `{soar}`, `{sorcery}`, `{winget}`, `{xbps}` |
| Extra | `{pacman-branch}` — the pacman branch name on Manjaro-like distributions |
| Totals | `{brew-all}`, `{flatpak-all}`, `{guix-all}`, `{hpkg-all}`, `{nix-all}`, `{all}` |

A variable for a manager that is not installed prints `0`, so a format that lists every manager
produces a long line of zeros rather than being skipped. There is no conditional syntax that
filters on "greater than zero" other than the general `{?var}…{?}` form.

Note that `{all}` is the sum of the individual counters, which is why `combined` has no
counterpart here: `{brew-all}` and `{brew}` + `{brew-cask}` are the same number.

## JSON output

```jsonc
{
    "type": "Packages",
    "result": {
        "all": 436,
        "brew": 414,
        "brewCask": 22
    }
}
```

Every key is camelCase. Zero-valued managers are **omitted**, and `all` is always present even
when it is `0`. `pacmanBranch` appears only when a branch was detected:

```jsonc
{
    "type": "Packages",
    "result": {
        "all": 54,
        "pacman": 51,
        "appimage": 3,
        "pacmanBranch": "stable"
    }
}
```

## Examples

```jsonc
// Only the total, and skip the slow detectors
{ "type": "packages", "disabled": ["nix", "flatpak", "guix"], "format": "{all}" }
```

```jsonc
// Collapse the sub-managers into one number each
{ "type": "packages", "combined": true }
```

## Pitfalls

- **The text output and the JSON output disagree when nothing is found.** With every manager
  either disabled or empty, the printed module reports
  `No packages from known package managers found`, while `--format json` emits
  `{ "all": 0 }` and no error at all. A script that checks for the error string will not see it
  in JSON mode.
- **A typo in `disabled` is silently ignored.** The parser matches names with a `switch` on the
  first letter and falls through without an error when nothing matches, so
  `"disabled": ["brewz"]` disables nothing and prints no diagnostic — even with
  `display.showErrors` set to `true`.
- **`combined` has no effect on JSON.** It only rewrites the human-readable line. A config that
  sets `combined: true` and then reads the JSON still gets `brew` and `brewCask` separately.
- **Some detectors are slow, and the schema says so.** `nix` shells out to `nix-store --query
  --hash` and, on a cache miss, to `nix-store --query --requisites`; `winget` runs
  `winget list` (about 1.5 s) and on Windows there is no cheap alternative. Add the slow ones to
  `disabled` if the module is on the hot path of a `--dynamic-interval` run.
- **Counts come from databases and directories, not from the package managers.** A package that
  is installed but not registered in the queried database is invisible, and a database entry left
  behind by a failed uninstall is counted as installed. Directory-based counters such as brew's
  (`Cellar`, `Caskroom`) count subdirectories, so a half-removed package still counts.
- **`flatpak` deliberately counts more than `flatpak list` does.** The runtime tree is walked as
  well as the app tree, with `.Locale` and `.Debug` entries excluded to match what
  `flatpak list` shows. Deleted apps are skipped by requiring a `current` symlink to exist.
- **The `rpm` count needs an SQLite RPM database.** On Linux it runs
  `SELECT count(*) FROM Sigmd5` against `/var/lib/rpm/rpmdb.sqlite`, which exists only on RPM
  releases that use the SQLite backend. On a Berkeley DB system the query returns `0` and the
  module falls back to `librpm` **only if fastfetch was built with RPM support**; otherwise the
  count is simply `0`. `Sigmd5` is chosen because it is the one table without the virtual
  `gpg-pubkey` package.
- **`dpkg` counts a status string, not packages.** `Status: install ok installed` is counted with
  `memmem()`, so a package in any other state (`deinstall ok config-files`, `install ok
  unpacked`) is not counted. The same string-counting approach is used for `apk` (`C:Q`), `crux`
  (blank lines), `opkg` (`Package:`) and `sorcery` (`:installed:`).
- **`snap` subtracts one for `/snap/bin`.** The first candidate is `/snap`, which contains the
  `bin` directory alongside the revision directories, so the result is decremented. The fallback
  path `/var/lib/snapd/snap` has no such directory and is not decremented.
- **Counts are cached on database modification times.** For the SQLite-backed managers the cache
  key is the database's mtime, so a database touched without a content change is re-counted, and
  one whose content changed without an mtime change keeps a stale value. `nix` uses
  `nix-store --query --hash` as its key instead, and `winget` uses a fingerprint of four registry
  keys. The cache files live in `<cacheDir>/fastfetch/packages/`, e.g.
  `~/.cache/fastfetch/packages/rpm.txt`, and a missing database is never cached (it means "no
  packages installed", which is not worth persisting).
- **`disabled` is not the only switch.** Builds may already have managers removed at compile
  time (`-DPACKAGES_REMOVE_DISABLED=ON`), in which case the key does not exist and no error is
  printed for it — it is simply not a known key any more.

## Implementation

### Linux, Android and GNU/Hurd

`ffDetectPackagesImpl()` builds a base path from `FASTFETCH_TARGET_DIR_ROOT` and dispatches on the
distribution id: when `ffDetectOS()->id` is `bedrock`, `getPackageCountsBedrock()` walks
`/bedrock/strata/*` and runs the regular scan once per stratum, otherwise
`getPackageCountsRegular()` runs a single scan. Each manager is guarded by
`FF_PACKAGES_IS_ENABLED(options, NAME)`, which is either a test of the `disabled` bitmask or — in
a `PACKAGES_REMOVE_DISABLED` build — a compile-time test of `FF_PACKAGES_DISABLE_LIST`.

The counting primitives are:

| Primitive | Used for |
|---|---|
| `ffPackagesGetNumElements(dir, isdir)` | directory listings, optionally counting only directories or only regular files |
| `getNumStrings(file, needle)` | counting occurrences of a fixed string with `memmem()` |
| `getSQLite3Int(db, query)` | a `count(*)` query through the dynamically loaded `libsqlite3` |
| `getNumElementsBySuffix(dir, suffix)` | files ending in `.appimage` |
| `countFilesRecursive(dir, filename)` | paludis, which counts `environment.bz2` files under `repositories` |

`pacman` additionally reads `DBPath` and `RootDir` from `/etc/pacman.conf` so a relocated
database is still found, and `pacmanBranch` comes from `Branch =` in `/etc/pacman-mirrors.conf`,
defaulting to `stable` when the key is present but empty. `am` checks `/opt/am/APP-MANAGER`
(counting the manager itself as a package) or, for the user variant, the directory named by
`appman-config` under the first XDG config directory. `guix` counts the **unique** `/gnu/store/`
hashes in a profile's `manifest` after a `qsort()`/`memcmp()` deduplication pass.

The user-scoped managers are detected from the home directory rather than the root:
`~/.nix-profile`, `$XDG_STATE_HOME/nix/profile` (or `~/.local/state/nix/profile`),
`/etc/profiles/per-user/$USER`, `~/.guix-profile`, `~/.guix-home/profile`,
`~/.local/share/flatpak`, `~/.local/share/soar/db/soar.db`, `~/AppImages` and `~/Applications`.

When the SQLite `rpm` query returns `0` and the build has `FF_HAVE_RPM`, `getRpmFromLibrpm()`
loads `librpm` dynamically, silences its logging with `rpmlogSetMask(RPMLOG_MASK(RPMLOG_EMERG))`,
and counts the `RPMDBI_LABEL` iterator. This is what covers openSUSE, whose database format the
SQLite query cannot read. It is deliberately skipped on Bedrock, where it does not work.

### macOS

`brew` is counted from `$HOMEBREW_PREFIX` (`Cellar` for formulae, `Caskroom` for casks), falling
back to Homebrew's own default prefix: the Apple-silicon one on an aarch64 build, `/usr/local`
otherwise. `macports` comes from
`$MACPORTS_PREFIX/var/macports/software`, defaulting to `/opt/local`. `nix` is delegated to
`packages_nix.c` for the default, system and user profiles.

### BSDs and Solaris

- **FreeBSD** reads two SQLite databases: `/var/db/pkg/local.sqlite` for `pkg` and
  `/var/db/mport/master.db` for `mport`, both with `SELECT count(*) FROM packages`.
- **NetBSD** counts the entries of `/usr/pkg/pkgdb`, **OpenBSD** those of `/var/db/pkg`.
- **Solaris** reads `/var/pkg/state/installed/catalog.attrs` as JSON and takes the
  `package-count` field for `pkg`, plus `/usr/pkg/pkgdb` for `pkgsrc`.

### Haiku

`hpkg` is counted from `/system/packages` and `/boot/home/config/packages` (regular files, not
directories). The source carries a TODO noting that the Package Kit C++ API would be more
accurate, since the directory listing also counts packages that have been disabled.

### Windows

`scoop` reads `~/.config/scoop/config.json` for `root_path` and `global_path`, falling back to
`~/scoop` and `%ProgramData%/scoop`, and counts the subdirectories of `apps/` — ignoring the
`scoop` entry itself. `choco` counts directories under `$ChocolateyInstall/lib`, and `pacman`
counts directories under `$MSYSTEM_PREFIX/../var/lib/pacman/local` on MSYS2.

`winget` is the only manager whose count cannot be read from a local index, because `winget list`
has to spawn a process. Its cache key is a fingerprint: the newest `LastWriteTime` among the ARP
uninstall keys (both the 64-bit and, in a 64-bit build, the 32-bit view, plus the per-user one)
and the MSIX package registry. `winget`'s own `installed.db` is explicitly rejected as a source —
the source comments record that on a machine with 101 listed packages it holds only 46 ids,
because it is a `PackageTrackingCatalog` rather than a snapshot, and that uninstalling through
Settings > Apps does not touch it at all. The command run is
`winget list --source winget --disable-interactivity --accept-source-agreements`, with the two
flags explained in the source: `--source winget` avoids listing ARP/MSIX packages that winget did
not install and skips the slow msstore round-trips, and `--accept-source-agreements` prevents a
first-run exit code of 1. A failed run is not cached. Directory enumeration on Windows goes
through `NtQueryDirectoryFile` with a 64 KiB buffer, and subtracts 2 from a directory count to
account for `.` and `..`.

### Shared

`ffDetectPackages()` calls the platform implementation and then sums every counter up to the
`all` field, using `offsetof(FFPackagesResult, all)` so that adding a manager only requires
putting its field before `all` in the struct. `packages.h` defines
`FF_PACKAGES_IS_ENABLED(options, NAME)` twice: as a bitmask test on `options->disabled` in a
normal build, and as a constant test of `FF_PACKAGES_DISABLE_LIST` in a
`PACKAGES_REMOVE_DISABLED` build, where the disabled managers are not compiled at all. A stub
overload of the SQLite helper returns `0` when fastfetch was built without `libsqlite3`.
