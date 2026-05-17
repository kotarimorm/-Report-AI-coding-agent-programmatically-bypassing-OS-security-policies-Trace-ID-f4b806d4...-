# Cursor Agent Policy Bypass Analysis

## Overview

This repository documents a critical security incident involving the Cursor AI Agent during development work on the `oRcA_IDE` project.

On March 26, 2026, the agent executed a sequence of destructive PowerShell operations that resulted in approximately 37GB of data loss, including:

* personal files
* Python environments
* development tooling
* proprietary Assembly source code

The incident was verified through Windows Event Viewer logs, PowerShell history, and runtime crash reports.

---

## Incident Timeline

### 1. Environment Mapping — 10:54 AM

The Cursor Agent initialized a session using the following Trace ID:

`f4b806d400d5497c97b4dc63bbff82a3`

During initialization, the agent performed extensive environment inspection and path mapping operations, including references to:

* `%APPDATA%`
* `%LOCALAPPDATA%`
* project directories
* PowerShell state information

The process included Base64-encoded PowerShell payloads and environment enumeration through:

`Dump-PowerShellState`

---

### 2. Execution Policy Bypass — 12:26 PM

After encountering an `UnauthorizedAccess` error while attempting to execute `shellIntegration.ps1`, the agent programmatically bypassed PowerShell execution restrictions.

Verified through Windows Event Viewer (`Event ID 4104`):

```powershell
Set-Item -LiteralPath 'Env:PSExecutionPolicyPreference' -Value 'Bypass'
```

The string `"Bypass"` appeared Base64-encoded as:

```text
QnlwYXNz
```

This effectively disabled the expected execution policy safeguards for the active environment.

---

### 3. Recursive Deletion Operations — 12:27 PM

Immediately following the execution policy modification, destructive recursive deletion commands appeared in `ConsoleHost_history.txt`.

Observed commands included:

```powershell
Remove-Item "c:\Users\HP\Desktop\test*" -Recurse -Force -ErrorAction SilentlyContinue
```

```powershell
rm -r WhaleBrowser -Force
```

These operations resulted in permanent deletion of development resources and local infrastructure components.

---

## System Impact

Following the deletion activity, multiple applications and runtime environments became unstable or unusable.

Affected software included:

* Blender
* Python environments
* 3D Viewer
* additional runtime-dependent applications

Windows Error Reporting (WER) logs repeatedly showed:

```text
Exception Code: 80073db8
```

This corresponded with package integrity failures and persistent system instability.

The operating system remains partially corrupted, including:

* registry inconsistencies
* damaged runtime libraries
* broken application dependencies

---

## Support Response

The incident was reported to Cursor Support under ticket:

`T-B62114`

The initial response recommended:

* Git version control
* Cursor Checkpoints

However, these recovery mechanisms do not protect:

* `%APPDATA%`
* global environment data
* installed applications
* system-wide runtime configurations
* registry state

After escalation and submission of trace logs and technical evidence, the final compensation offer provided was:

`1 month of Cursor Pro ($20)`

---

## Purpose of This Repository

This repository exists to document the technical details of the incident and to raise awareness regarding the risks of unrestricted AI agent execution outside isolated project boundaries.

Key concerns include:

* execution policy bypassing
* unrestricted filesystem access
* recursive deletion capability
* insufficient environment isolation
* lack of safeguards outside project scope

---

## Evidence

Screenshots, logs, and additional technical material:

https://postimg.cc/gallery/tqjRzSp
