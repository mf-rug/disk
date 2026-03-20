# disk

A pretty disk usage analysis tool for macOS. One command to see what's eating your storage.

![Python 3](https://img.shields.io/badge/python-3.8+-blue) ![macOS](https://img.shields.io/badge/platform-macOS-lightgrey) ![No dependencies](https://img.shields.io/badge/dependencies-none-green)

## Install

```bash
curl -o ~/.local/bin/disk https://raw.githubusercontent.com/mf-rug/disk/master/disk
chmod +x ~/.local/bin/disk
```

Make sure `~/.local/bin` is in your `PATH`:

```bash
export PATH="$HOME/.local/bin:$PATH"  # add to your .bashrc / .zshrc
```

## Usage

### Disk overview

```
$ disk

  ★ Macintosh HD — Internal SSD (Apple Fabric)
  ██████████████████████████░░░░  851 GB / 995 GB used (86%)
  107 GB available

  Backup Drive — External (USB)
  ████████░░░░░░░░░░░░░░░░░░░░░░  320 GB / 1.0 TB used (32%)
  680 GB available
```

Shows all connected physical disks with color-coded usage bars. The boot volume is marked with ★. Filters out system partitions, APFS snapshots, and virtual mounts.

### Storage breakdown (`--details` / `-d`)

```
$ disk -d

  Storage Breakdown
  ──────────────────────────────────────────────────────────────
  Photos             220.3 GB  █████░░░░░░░░░░░   22.1%  84.2k files
  Documents          176.8 GB  ███░░░░░░░░░░░░░   17.8%  64.0k files
  macOS System       114.7 GB  ██░░░░░░░░░░░░░░   11.5%
  Applications        64.5 GB  █░░░░░░░░░░░░░░░    6.5%  42.1k files
  Caches & App Data   16.9 GB  ░░░░░░░░░░░░░░░░    1.7%  31.6k files
  ...
  ──────────────────────────────────────────────────────────────
  Free Space         130.9 GB  ░░░░░░░░░░░░░░░░   13.2%
```

Similar to macOS System Settings > Storage. Shows where your space goes, with file counts. Sums to 100%.

### Cleanup suggestions (`--clean` / `-c`)

```
$ disk -c

  Cleanup Opportunities
  ─────────────────────────────────────────────────────────────────
  Safe =auto-regenerated cache    Review =check first    Caution =inspect before deleting
  ─────────────────────────────────────────────────────────────────
  [Safe]    Homebrew cache              3.2 GB
            Downloaded bottles and source archives
            brew cleanup --prune=all

  [Review]  DMGs in Downloads         509.6 MB
            Disk images — usually safe to delete after installing apps
            rm ~/Downloads/*.dmg

  [Caution] ZIPs in Downloads           4.3 GB
            Could be anything — review before bulk-deleting
            ls -lhS ~/Downloads/*.zip  (review, then delete selectively)
  ─────────────────────────────────────────────────────────────────
  Total reclaimable                   18.4 GB
```

Scans for reclaimable space across caches, downloads, build artifacts, and more. Each suggestion is color-coded by risk level with a ready-to-run cleanup command.

**What it checks:**

| Category | Targets |
|----------|---------|
| Downloads | `.dmg`, `.zip`, `.pkg` files |
| Package managers | Homebrew, pip, npm, Conda, Cargo, Gradle, Go modules |
| Browsers | Chrome, Firefox, Brave, Safari, Edge caches |
| Apps | Electron app caches (VS Code, Slack, Discord, Teams), Zoom |
| Xcode | DerivedData, iOS DeviceSupport |
| System | `~/Library/Caches/*`, `~/Library/Logs`, crash reports, `.Trash` |
| iOS backups | Smart duplicate detection — only suggests removing older backups |
| Docker | Reclaimable images and build cache |

### Biggest files (`--big` / `-b`)

```
$ disk -b

  Biggest files in /
  ──────────────────────────────────────────────────────────────────────
     18.0 GB  1.2y ago  ~/Downloads/ubuntu-24.04-desktop-amd64.iso
      5.6 GB  2.1y ago  ~/Movies/vacation-2024.mov
      2.4 GB   5mo ago  ~/Library/Android/sdk/system-images/android-35/system.img
  ──────────────────────────────────────────────────────────────────────
```

Finds the 20 largest files. Color-coded by age — older files (yellow >6mo, red >1yr) are more likely forgotten. Skips cloud storage (Google Drive, OneDrive, Dropbox, iCloud) and system volumes.

### Directory breakdown

```
$ disk ~/Projects

  /Users/jane/Projects
  48.2 GB total, 124.5k files

  ──────────────────────────────────────────────────────────
  webapp            22.1 GB  ███████░░░░░░░░░░   45.9%  68.3k files
  ml-models         14.8 GB  █████░░░░░░░░░░░░   30.7%  1.2k files
  scripts            6.4 GB  ██░░░░░░░░░░░░░░░   13.3%  12.4k files
  Other              4.9 GB  ░░░░░░░░░░░░░░░░░    2.8%  3.7k files
  ──────────────────────────────────────────────────────────
```

Drill into any directory. Small subdirectories are grouped into "Other".

## Requirements

- macOS (uses `diskutil`, `df`, `du`)
- Python 3.8+
- No external dependencies — stdlib only

## License

MIT
