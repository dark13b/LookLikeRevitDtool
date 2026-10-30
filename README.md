# RevitDtools

Revit add-in inspired by DTools for converting DWG geometry to detail lines and
creating structural columns from lines.

[![Revit 2026](https://img.shields.io/badge/Revit-2026-blue.svg)](https://www.autodesk.com/products/revit)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)

## Tools

- DWG-to-detail-line conversion and geometry processing.
- Column creation from lines, batch creation, circular columns and grids.
- Family diagnostics, standard family loading and settings.

## Build and validation status

The project targets Revit 2026, .NET 8 and Windows x64. Building requires the
Revit 2026 API assemblies. Both project files currently reference a local
`E:\Program Files\Autodesk\Revit 2026` installation; adjust those paths for your
machine before building.

```powershell
git clone https://github.com/dark13b/LookLikeRevitDtool.git
cd LookLikeRevitDtool
dotnet build RevitDtools.csproj --configuration Release
dotnet test RevitDtools.Tests/RevitDtools.Tests.csproj --configuration Release
```

The latest local baseline build failed because the configured API assemblies
were unavailable. Runtime behavior and the full test suite remain unverified.
See the [assessment and repair plan](wiki/2026-10-31-assessment.md).

## Installation

The `.addin` manifest currently contains a developer-specific DLL path. Set it
to the built assembly location before installing it under
`%APPDATA%\Autodesk\Revit\Addins\2026`. Include runtime dependencies alongside
the assembly. The existing MSI configuration needs repair before distribution.

Historical v1.1.0 packages are no longer stored in the source tree. They remain
recoverable from Git history; see the [cleanup log](wiki/2026-10-31-cleanup.md).

## Repository layout

| Path | Contents |
| --- | --- |
| `DtoolsCommands.cs` | Application entry point, ribbon and original commands |
| `Core/` | Commands, services, models and interfaces |
| `UI/` | WPF windows and command availability |
| `Utilities/` | Logging, validation and error handling |
| `RevitDtools.Tests/` | MSTest project |
| `Deployment/` | Installer sources and deployment validation |
| `Documentation/` | User guides and historical known issues |
| `wiki/` | Indexed change log, assessments and cleanup records |

## Documentation

- [Wiki index and change log](wiki/README.md)
- [User manual](Documentation/UserManual.md)
- [Quick start](Documentation/QuickStartGuide.md)
- [Historical known issues](Documentation/KNOWN_ISSUES.md)
- [Report an issue](https://github.com/dark13b/LookLikeRevitDtool/issues)

## License

Current source: [Apache License 2.0](LICENSE), with attribution in [NOTICE](NOTICE).
Previously published v1.1.0 artifacts retain their original MIT license.
