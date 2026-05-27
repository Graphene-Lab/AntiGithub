# DataRedundancy – Developer Guide

## Integration

```csharp
using DataRedundancy;

var redundancy = new Git(
	localPath:  @"C:\Users\alice\Projects\MyApp",
	remotePath: @"\\server\shared\git\MyApp"   // UNC or local path
);

// Start real-time monitoring
redundancy.StartMonitoring();
```

## Constructor Parameters

| Parameter | Type | Description |
|---|---|---|
| `localPath` | `string` | Local directory to monitor and sync |
| `remotePath` | `string` | Shared network mirror path |

## Stopping Monitoring

```csharp
redundancy.StopMonitoring();
```

## Merge Behaviour

The `Merge` class handles conflict resolution. Default behaviour:

- **Text additions**: automatically merged (union of all additions from all users).
- **Source code**: only propagated when the local build succeeds.
- **Conflicting edits of the same line**: last-write wins.

To prevent in-progress code from being propagated to other users, wrap it with a conditional compilation symbol:

```csharp
#if ALICE_WIP
// Work in progress — not propagated until this block is removed
public void NewFeature() { ... }
#endif
```

## Events

```csharp
redundancy.OnFileUpdated += (filePath, source) =>
{
	Console.WriteLine($"Updated: {filePath} from {source}");
};

redundancy.OnMergeConflict += (filePath) =>
{
	Console.WriteLine($"Merge conflict (last-write wins): {filePath}");
};
```

## VPN / Remote Git Directory

For teams distributed across locations, mount the shared directory via VPN and pass the VPN UNC path as `remotePath`. DataRedundancy works identically over VPN.

## Localisation

The library reads `CultureInfo.CurrentUICulture` at startup and loads the matching resource file (EN, DE, ES, FR, IT).

## Building

```powershell
dotnet build ..\..\AntiGit\DataRedundancy\DataRedundancy.csproj
```
