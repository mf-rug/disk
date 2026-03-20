# disk

A pretty disk usage analysis tool for macOS. One command to see what's eating your storage.

![Python 3](https://img.shields.io/badge/python-3.8+-blue) ![macOS](https://img.shields.io/badge/platform-macOS-lightgrey) ![No dependencies](https://img.shields.io/badge/dependencies-none-green)

## Install

```bash
curl -o ~/.local/bin/disk https://raw.githubusercontent.com/maxfurst/disk/main/disk
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

  webdav.data.rug.nl — Network
  (usage unavailable)
```

Shows all connected physical disks with color-coded usage bars. The boot volume is marked with ★. Filters out system partitions, APFS snapshots, and virtual mounts.

### Storage breakdown (`--details` / `-d`)

```
$ disk -d

  Storage Breakdown
  ──────────────────────────────────────────────────────────────
  Pictures           320.3 GB  █████░░░░░░░░░░░   32.2%  146.9k files
  Documents          176.8 GB  ███░░░░░░░░░░░░░   17.8%   64.0k files
  macOS System       114.7 GB  ██░░░░░░░░░░░░░░   11.5%
  Applications        64.5 GB  █░░░░░░░░░░░░░░░    6.5%  571.8k files
  Caches & App Data   16.9 GB  ░░░░░░░░░░░░░░░░    1.7%  211.6k files
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
  [Safe]    Conda package cache         9.5 GB
            Cached conda packages — can be re-downloaded
            rm -rf ~/anaconda3/pkgs

  [Review]  DMGs in Downloads         509.6 MB
            Disk images — usually safe to delete after installing apps
            rm ~/Downloads/*.dmg

  [Caution] ZIPs in Downloads           4.3 GB
            Could be anything — review before bulk-deleting
            ls -lhS ~/Downloads/*.zip  (review, then delete selectively)
  ─────────────────────────────────────────────────────────────────
  Total reclaimable                   36.2 GB
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
     18.0 GB  1.2y ago  ~/Documents/code/wikigrep/enwiki-latest-pages-articles.xml.bz2
      7.0 GB  1.4y ago  ~/Documents/work/Teaching/.syncthing.SB_Transcription.mov.tmp
      5.6 GB  1.2y ago  ~/Documents/code/wikigrep/enwiki-latest-pages-articles.xml.bz2.2
  ──────────────────────────────────────────────────────────────────────
```

Finds the 20 largest files. Color-coded by age — older files (yellow >6mo, red >1yr) are more likely forgotten. Skips cloud storage (Google Drive, OneDrive, Dropbox, iCloud) and system volumes.

### Directory breakdown

```
$ disk ~/Documents

  /Users/max/Documents
  176.7 GB total, 64.0k files

  ──────────────────────────────────────────────────────────
  work              86.4 GB  ████████░░░░░░░░   48.9%  38.4k files
  Vuze Downloads    38.7 GB  ████░░░░░░░░░░░░   21.9%  292 files
  code              29.2 GB  ███░░░░░░░░░░░░░   16.5%  21.5k files
  Other              4.9 GB  ░░░░░░░░░░░░░░░░    2.8%  3.7k files
  ──────────────────────────────────────────────────────────
```

Drill into any directory. Small subdirectories are grouped into "Other".

## Requirements

- macOS (uses `diskutil`, `df`, `du`)
- Python 3.8+
- No external dependencies — stdlib only

## License

MIT
