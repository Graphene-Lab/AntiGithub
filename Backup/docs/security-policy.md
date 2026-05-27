# BackupLibrary – Security Policy

## Backup Data Protection

The backup library monitors paths and copies data to backup destinations. If the backup destination is remote or shared:

- Use encrypted transport (SFTP, HTTPS) for remote backup destinations.
- Apply permissions so only the backup service account can write to the backup destination.
- Enable encryption at rest on the backup destination volume.

## Path Monitoring Security

`PathMonitoring.cs` watches file system paths for changes. Ensure:

- The monitored paths do not include system directories containing sensitive OS credentials.
- The service account has read-only access to monitored paths where possible.

## Backup Integrity

To detect backup corruption or tampering:

- Enable hash verification of backup files after writing.
- Periodically perform restore tests to verify backup integrity.

## OS Support (`OSSupport.cs`)

OS-level integration (e.g., shadow copy on Windows, snapshot on Linux) may require elevated privileges. Run only the minimum required operations as elevated; perform all other backup logic as a standard user.

## Sensitive File Exclusions

Exclude files containing secrets (private keys, passwords, certificates) from backups stored in untrusted destinations, or ensure those backups are separately encrypted with a key held only by authorised parties.

## Reporting Vulnerabilities

Open a private GitHub Security Advisory in the repository. Do not disclose vulnerabilities publicly before a fix is available.
