# AntiGitLibrary – System Overview

## Purpose

**AntiGitLibrary** is a thin orchestration library that bridges a user-facing application with the `DataRedundancy` and `BackupLibrary` subsystems. It provides a unified entry point (`Context`) to initialize and coordinate:

- **Real-time file mirroring** to a local or remote network path (DataRedundancy).
- **Automatic backup** (daily + per-file versioning) via hard links (BackupLibrary).

The name "AntiGit" reflects the project's philosophy: provide the *collaborative benefits of Git* (automatic merge, shared file propagation) without the manual workflow overhead — everything happens transparently, in real time.

## Architecture

```
Application (UI / service)
	│
	▼
AntiGitLibrary.Context  (unified entry point)
	│
	├── DataRedundancy  (real-time mirroring + intelligent merge)
	└── BackupLibrary   (daily backup + versioning via hard links)
```

## When to Use AntiGitLibrary

Use `AntiGitLibrary` when you need to:
- Add real-time file synchronization and backup to an application with minimal integration effort.
- Keep multiple users' local copies of shared files automatically in sync via a shared network path.
- Provide versioning without a full VCS, and without requiring users to learn Git.

## Target Framework

**.NET Standard 2.1**
