# BackupLibrary – Developer Guide

## Integration

```csharp
using BackupLibrary;

var backup = new Backup(
	sourcePath: @"C:\Users\alice\Documents",
	backupRoot: @"D:\Backups\Documents"
);

// Start monitoring for versioning backups
backup.StartMonitoring();

// Trigger a manual daily backup immediately
backup.RunDailyBackup();
```

## Constructor Parameters

| Parameter | Type | Description |
|---|---|---|
| `sourcePath` | `string` | Directory to back up |
| `backupRoot` | `string` | Root directory where backup snapshots are stored |

## Scheduling

BackupLibrary does **not** include a built-in scheduler. Integrate with the host's task scheduler:

- **Windows**: use `System.Threading.Timer` or Windows Task Scheduler.
- **Linux/macOS**: use `cron` or `systemd` timers to call `RunDailyBackup()`.

For versioning, `StartMonitoring()` uses `FileSystemWatcher` internally and fires automatically on each file change.

## Stopping Monitoring

```csharp
backup.StopMonitoring();
```

## Accessing Backup Snapshots

Backup snapshots are plain directories. Each snapshot folder is named after the backup date/time:

```
backupRoot/
├── 2024-01-15/
├── 2024-01-15T14-32-05/
└── ...
```

To restore a file, simply copy it from the relevant snapshot directory.

## Space Estimation

Because hard links are used for unchanged files, the disk space consumed by N snapshots is approximately:

```
total_space ≈ size_of_all_files + (N-1) × average_daily_change_size
```

## OS Support Details

`OSSupport.cs` wraps the platform-specific hard-link creation:

| Platform | API used |
|---|---|
| Windows | `CreateHardLink` (kernel32.dll) |
| Linux/macOS | `link()` POSIX syscall via P/Invoke |

## Building

```powershell
dotnet build ..\..\AntiGit\Backup\BackupLibrary.csproj
```
