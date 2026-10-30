# Source assessment - 2026-10-31

Scope: source review at baseline `51da317`, plus a local Release build on Windows
with .NET SDK 10.0.402. These are repair candidates, not verified runtime fixes.

## Findings and acceptance checks

| Priority | Evidence | Repair | Verify |
| --- | --- | --- | --- |
| P0 | `RevitDtools.csproj` and the test project hardcode API DLLs under `E:\Program Files\Autodesk\Revit 2026`. That path is absent here. | Use a shared overridable Revit installation path, validate both DLLs before compilation, and build against the actual supported version. | Build production and tests against Revit 2026; distinguish missing references from remaining source errors. |
| P0 | Root `RevitDtools.addin` points to a developer-specific absolute DLL path. `Deployment/RevitDtools.wxs` references absent inputs including `LICENSE.txt`, PDF manuals, version-specific add-ins and `CustomActions.dll`. | Produce a self-contained Revit 2026 package with a manifest resolving to the installed assembly, its dependencies, LICENSE and NOTICE. | Install on a clean Windows user account, restart Revit and execute a command. |
| P0 | `BatchProcessingService.ProcessImportedGeometry` sends `ProcessElement(importedElement)` to `Task.Run`. `GeometryProcessingService.ProcessElement` reads Revit geometry and its processors create Revit elements. | Keep Revit API calls inside a valid Revit entry point. Use an ExternalEvent for modeless work; background work may operate only on detached plain data. Audit the whole transaction and await chain. | Import representative DWGs inside Revit with no cross-thread or API-context exceptions; verify rollback on failure. |
| P1 | `CancelProcessing` cancels `_cancellationTokenSource`, but the processing loop observes the caller's token instead. `ValidateFiles` silently removes invalid inputs from per-file results. | Link the cancellation sources per run; preserve one outcome per requested input, including invalid and cancelled files. | Cancel through each public entry point, then run again; mixed valid/missing inputs must reconcile requested, completed, failed and cancelled counts. |
| P1 | `GeometryProcessingService.ProcessNestedBlock` calls `GetInstanceGeometry` and also multiplies `block.Transform`. Other extraction paths do the same. | Review coordinate-space handling before changing it; choose instance geometry or symbol geometry with its transform consistently. | Revit fixtures with translated, rotated, scaled and nested DWG blocks must produce correct endpoints, counts and layers without duplicate transforms. This remains a hypothesis until tested. |
| P1 | `UpdateService` uses `revitdtools/revitdtools`, rather than this repository, and does not configure a GitHub User-Agent. | Correct the endpoint, request headers and release-version metadata; handle no release and failed network responses without interrupting Revit. | Stub the HTTP responses for newer/current versions, 404, rate limits, malformed payloads and timeout. |
| P1 | The solution contains only the production project. `RevitDtools.Tests/SimpleTest.cs` asserts `true`; standalone diagnostic runners also exist. | Include the real test project in verification, replace placeholder coverage with behavior checks, and separate tests requiring Revit from detached logic tests. | Explicitly run the test project and report discovery/pass/fail/skip counts; run host-dependent workflows inside Revit. |

## Recommended order

1. Restore a reproducible Revit 2026 build and clean-machine installation.
2. Correct API context, transactions and cancellation before adding features.
3. Validate geometry and column placement against small known Revit/DWG fixtures.
4. Improve user feedback: preview counts and dimensions, explain rejected geometry,
   and export a per-item outcome report. Extend existing result models rather than
   introduce a new framework.

## Checks performed and limits

- `dotnet build RevitDtools.csproj --configuration Release --nologo -v:q` failed
  with 42 warnings and 523 errors, including unresolved Revit references. These
  counts are baseline diagnostics, not 523 independent confirmed defects.
- No Revit 2026 session, DWG import, column placement, MSI build, updater network
  request or test suite was validated. README claims of prior fixes are not new
  acceptance evidence.
- This change updates licensing and documentation only. The existing release ZIP,
  DLL and release README remain historical MIT artifacts; create a new versioned
  release with Apache notices after runtime validation.

## Primary references

- [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0.txt)
- [Autodesk: Revit API deployment and single-threaded access](https://help.autodesk.com/cloudhelp/2026/ENU/Revit-API/files/Revit_API_Developers_Guide/Introduction/Getting_Started/Using_the_Autodesk_Revit_API/Revit_API_Revit_API_Developers_Guide_Introduction_Getting_Started_Using_the_Autodesk_Revit_API_Deployment_Options_html.html)
- [Autodesk: External Events](https://help.autodesk.com/cloudhelp/2026/ENU/Revit-API/files/Revit_API_Developers_Guide/Advanced_Topics/Revit_API_Revit_API_Developers_Guide_Advanced_Topics_External_Events_html.html)
