# DataRedundancy – Security Policy

## Data Integrity

DataRedundancy replicates data across multiple storage locations using Git-based versioning (`Git.cs`). Each replica is independently verifiable via Git's SHA-1/SHA-256 object model.

## Repository Access Control

- Remote Git repositories should be protected with SSH key authentication or HTTPS with access tokens.
- Do not store credentials in repository configuration files. Use the OS credential store or environment variables.
- Restrict push access to redundancy replicas to the service account running the DataRedundancy process.

## Conflict Resolution

`Merge.cs` handles conflicting changes between replicas. Merge operations:

- Are logged for audit purposes.
- Do not silently discard data — conflicts produce a merged result that preserves both versions where possible.
- Should be reviewed by an administrator when they occur frequently (may indicate a configuration problem).

## Sensitive Data in Replicas

If the replicated dataset contains sensitive files, ensure:

- Remote repositories are encrypted at rest (e.g., via Git-crypt or full-disk encryption on the host).
- Access to remote repositories is restricted to authorised parties only.

## Network Security

Replication over a network should use encrypted transports only (SSH, HTTPS with valid certificates). Plain HTTP or unencrypted SSH is not acceptable for production.

## Reporting Vulnerabilities

Open a private GitHub Security Advisory in the repository. Do not disclose vulnerabilities publicly before a fix is available.
