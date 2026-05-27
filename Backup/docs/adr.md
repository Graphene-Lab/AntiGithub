# BackupLibrary – Architecture Decision Records

## ADR-001: Hard Links for Space-Efficient Snapshots

**Date**: 2024  
**Status**: Accepted

### Context
Daily full-copy backups of a large directory tree consume enormous disk space. Differential backups are space-efficient but complex to restore. Incremental backups require a chain of tapes/archives.

### Decision
Use **hard links** for unchanged files. Each backup snapshot is a directory that appears as a full copy, but unchanged files are hard links to the previous snapshot's inodes. Only modified files are copied as new data.

### Consequences
- **Positive**: snapshot appears as a full copy; restoring a specific version is a simple file copy.
- **Positive**: disk usage grows only by the size of changes, not by the size of the full tree.
- **Negative**: requires NTFS (Windows) or a POSIX file system (Linux/macOS) that supports hard links.
- **Negative**: hard links do not work across file-system boundaries; backup destination must be on the same or a compatible file system.

---

## ADR-002: Two Independent Backup Strategies

**Date**: 2024  
**Status**: Accepted

### Context
Users need both a scheduled daily recovery point (disaster recovery) and fine-grained version history (file-level rollback).

### Decision
Implement two complementary strategies in the same library:
1. **Daily backup**: triggered by a scheduler; one snapshot per day.
2. **Versioning backup**: triggered by `FileSystemWatcher` on every file save; timestamped snapshot.

Both strategies use the same hard-link mechanism.

### Consequences
- **Positive**: both needs covered by a single library with a consistent API.
- **Negative**: versioning backup can generate many snapshots on frequently-saved files; disk usage must be monitored.

---

## ADR-003: Cross-Platform via OSSupport Abstraction

**Date**: 2024  
**Status**: Accepted

### Context
Hard-link creation uses different OS APIs on Windows (`CreateHardLink` via P/Invoke) and Linux/macOS (`link()` syscall).

### Decision
Abstract OS differences in `OSSupport.cs`. The rest of the library calls `OSSupport.CreateHardLink(source, destination)` without knowing the underlying OS.

### Consequences
- **Positive**: the core backup logic is OS-agnostic.
- **Positive**: new platform support can be added by extending `OSSupport` alone.
- **Negative**: P/Invoke adds a native dependency on Windows; must be tested on each supported OS.
