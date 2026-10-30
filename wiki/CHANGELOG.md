# Change log

## 2026-10-31 - Repository cleanup

- Removed 25 obsolete/generated files and moved three files into their existing folders.
- Simplified README, consolidated documentation and ignored generated release packages.
- See the [cleanup record and verification](2026-10-31-cleanup.md).

## 2026-10-31 - Apache 2.0 and initial assessment

- Replaced the current source license with the canonical Apache 2.0 text.
- Preserved the existing copyright attribution in NOTICE.
- Updated the root README license badge and wording; added this indexed wiki.
- Retained historical v1.1.0 binaries, archive and release documentation unchanged.
- Reviewed build configuration, add-in deployment, batch processing, geometry processing,
  updater, installer and test discovery. Findings and acceptance checks are in the
  [assessment](2026-10-31-assessment.md).
- Baseline Release build failed: 42 warnings and 523 errors. The configured Revit
  2026 API DLL path does not exist on this machine. No Revit runtime acceptance
  or successful test run is claimed. No runtime fixes are included in this change.
