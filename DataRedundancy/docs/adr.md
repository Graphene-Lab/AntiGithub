# DataRedundancy – Architecture Decision Records

## ADR-001: Real-Time Mirroring via FileSystemWatcher

**Date**: 2024  
**Status**: Accepted

### Context
Periodic polling to detect file changes introduces latency and CPU overhead proportional to the number of files.

### Decision
Use .NET `FileSystemWatcher` to receive OS-level notifications as soon as a file is created, modified, or deleted.

### Consequences
- **Positive**: zero polling overhead; changes are propagated within milliseconds.
- **Negative**: `FileSystemWatcher` can miss events under very high I/O load; a reconciliation scan runs periodically as a safety net.

---

## ADR-002: Compilation Gate for Source Code Files

**Date**: 2024  
**Status**: Accepted

### Context
Propagating code that does not compile breaks other developers' builds.

### Decision
Hook into MSBuild post-build events. A source file is pushed to the remote mirror only if the project builds successfully after the change.

### Consequences
- **Positive**: other developers' builds are never broken by auto-propagated code.
- **Negative**: the compilation gate only works for .NET projects; binary file changes (images, JSON) are always propagated immediately.

---

## ADR-003: Last-Write Wins for Binary Files

**Date**: 2024  
**Status**: Accepted

### Context
Implementing a merge algorithm for arbitrary binary formats (images, Office documents, PDFs) is impractical.

### Decision
For binary files, apply **last-write wins**: the most recently modified version overwrites the remote mirror.

### Consequences
- **Positive**: simple, predictable behaviour for binary files.
- **Negative**: simultaneous edits to the same binary file by two users result in one user's changes being lost.

---

## ADR-004: Multi-Language Resource Files

**Date**: 2024  
**Status**: Accepted

### Context
The library is used in applications targeting European markets where English-only UI is insufficient.

### Decision
Provide `.resx` resource files for DE, ES, FR, IT, and EN. The correct resource is selected at runtime via `CultureInfo.CurrentUICulture`.

### Consequences
- **Positive**: the library is immediately usable in 5 languages without changes by the consumer.
- **Negative**: new user-visible strings require updates to all 5 resource files.
