# AntiGitLibrary – Architecture Decision Records

## ADR-001: Orchestration Library Pattern

**Date**: 2024  
**Status**: Accepted

### Context
`DataRedundancy` (mirroring + merge) and `BackupLibrary` (backup + versioning) are independent, reusable components. A consuming application should not need to initialise and coordinate them individually.

### Decision
Create `AntiGitLibrary` as a thin **orchestration layer** that initialises and wires together both subsystems through a single `Context` object.

### Consequences
- **Positive**: single entry point for all functionality.
- **Positive**: `DataRedundancy` and `BackupLibrary` remain independently usable without `AntiGitLibrary`.
- **Negative**: the orchestration layer adds a dependency; projects that need only backup must still reference `AntiGitLibrary` or use `BackupLibrary` directly.

---

## ADR-002: Compilation-Gate for Source Code Propagation

**Date**: 2024  
**Status**: Accepted

### Context
In a multi-developer environment, propagating non-compiling code breaks other developers' builds immediately.

### Decision
For source code file changes, `DataRedundancy` hooks into the MSBuild output to check whether the project compiles after the change. A file is only pushed to the shared mirror if the build succeeds.

### Consequences
- **Positive**: other developers never receive broken code via automatic sync.
- **Positive**: developers can work freely without coordinating commits.
- **Negative**: requires the host application to be a .NET project; the compilation gate is not available for non-.NET codebases.
