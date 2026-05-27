# AntiGitLibrary – Developer Guide

## Integration

Add a reference to `AntiGitLibrary` and create a `Context`:

```csharp
using AntiGitLibrary;

var context = new Context(
	localPath:   @"C:\Users\alice\Documents",
	gitPath:     @"\\server\shared\git",    // network mirror path
	backupPath:  @"D:\Backups"              // external drive for backups
);
```

## Constructor Parameters

| Parameter | Type | Description |
|---|---|---|
| `localPath` | `string` | The directory to monitor and sync |
| `gitPath` | `string?` | UNC or local path used as the shared mirror; `null` disables mirroring |
| `backupPath` | `string?` | Destination for daily/versioning backups; `null` disables backup |

## How Files Are Synced

1. `Context` starts a `FileSystemWatcher` on `localPath`.
2. On any file modification:
   - The changed file is copied to `gitPath` (the shared mirror).
   - All other connected users receive the update in real time (or on next connection).
3. Incoming changes from `gitPath` are merged into `localPath` using the intelligent merge algorithm.

## Merge Behaviour

- Text files: additions from multiple sources are merged automatically.
- For source code, merge only propagates when the file compiles successfully (integration with MSBuild events).
- Binary files: last-write wins.

## Backup

- **Daily backup**: triggered at midnight (configurable). Stores a full snapshot using hard links.
- **Versioning backup**: triggered on every file modification. Each version is stored with its timestamp.

Hard links ensure that unchanged files do not consume additional disk space.

## Disabling Individual Features

```csharp
// Mirroring only, no backup
var context = new Context(localPath, gitPath, backupPath: null);

// Backup only, no mirroring
var context = new Context(localPath, gitPath: null, backupPath);
```

## Building

```powershell
dotnet build ..\..\AntiGit\AntiGit\AntiGitLibrary.csproj
```
