# BackupLibrary – System Overview

## Purpose

**BackupLibrary** is a cross-platform library that provides two complementary backup strategies via a single, easy-to-integrate API:

1. **Daily backup** – a scheduled full-snapshot backup taken once per day.
2. **Versioning backup** – an incremental snapshot taken every time a file is modified.

Both strategies use **hard links** (or symbolic links as fallback) to avoid duplicating unchanged files, making the backup space-efficient while appearing as a complete copy to the user.

## How Intelligent Backup Works

```
Backup D-1  ─── hard link ──► Backup D-2 (unchanged files point to D-1 data)
								   │
								   └── new copy (only modified files)
```

1. On each backup cycle, the library compares the source directory tree with the previous backup snapshot.
2. **Modified files** → copied as new files.
3. **Unchanged files** → a hard link is created pointing to the previous backup's file.
4. The result is a directory that looks like a full copy but occupies only the space of the changes.

## Symbolic Links

A symbolic link is a file-system entry that transparently redirects to another path. BackupLibrary uses hard links (same inode) rather than symbolic links where possible, since hard links survive moves and renames of the target.

## Versioning

Every time a monitored file is modified, `PathMonitoring.cs` triggers a versioned snapshot named with the current timestamp:

```
backups/
├── 2024-01-15/          ← daily backup
├── 2024-01-15T14-32-05/ ← versioned snapshot (file save at 14:32)
└── 2024-01-15T16-07-11/ ← versioned snapshot (file save at 16:07)
```

## Platform Support

- **Windows**: uses NTFS hard links via P/Invoke.
- **Linux / macOS**: uses native `link()` system call.
- `OSSupport.cs` abstracts platform differences.

## Target Framework

**.NET Standard 2.1**
