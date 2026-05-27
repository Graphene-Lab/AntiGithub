# DataRedundancy – System Overview

## Purpose

**DataRedundancy** is a cross-platform library for real-time file mirroring and intelligent multi-user merge. It monitors a local directory and keeps a remote (network) directory in sync, propagating changes to all connected participants automatically.

It was created to fill a gap in collaborative workflows: the automatic, real-time merging of changes made by multiple users to shared files — something that traditional version control systems (including Git) require manual intervention to achieve.

## Core Features

| Feature | Description |
|---|---|
| **Real-time mirroring** | File changes are pushed to the shared network path as soon as they occur |
| **Intelligent merge** | Text additions from multiple users are merged automatically |
| **Compileable-code-only propagation** | Source code changes are only propagated when the result compiles |
| **Multi-user propagation** | All connected users receive updates immediately; offline users receive them on next connection |
| **Any file type** | Works with documents, source code, images, and any other file |
| **VPN support** | Remote git directory can be on a VPN-accessible server |

## Architecture

```
User A (local)          User B (local)
	│                       │
	▼                       ▼
DataRedundancy          DataRedundancy
	│                       │
	└──────► Shared Network Git Directory ◄──────┘
			  (UNC path / VPN mount)
```

## Merge Algorithm

1. A file modification on User A's machine is detected by `FileSystemWatcher`.
2. `Git.cs` computes the diff between the local version and the shared mirror version.
3. `Merge.cs` applies an intelligent 3-way merge:
   - Lines added by User A that are not in the shared version are preserved.
   - Lines added by User B (already in the shared version) are preserved.
   - Conflicting edits of the same line fall back to last-write.
4. The merged result is written to the shared directory and pushed to all online users.

## Compilation Gate (Source Code)

For source code files, `Support.cs` hooks into the MSBuild process. A file is only mirrored to other users if the local project compiles successfully after the change. This prevents non-compiling code from breaking other users' builds.

Developers can also hide in-progress code using `#if MYUSERNAME` preprocessor directives.

## Localisation

Resource files provide translations for DE, ES, FR, IT, EN.

## Target Framework

**.NET Standard 2.1**
