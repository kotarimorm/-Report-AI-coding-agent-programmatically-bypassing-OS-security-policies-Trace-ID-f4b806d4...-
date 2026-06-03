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

## Complete Technical Evidence & Case Timeline

### 1. Session Initialization & Trace Matching
The Cursor AI Agent initializes an obfuscated PowerShell session, injecting system-level parameters directly into the environment variables.
* **Timestamp:** 26.03.2026 11:52:38
* **Evidence:** Windows Event Viewer (`Event ID 4104`). Contains `Env:CURSOR_AGENT` and the unique session tracking token `Env:CURSOR_TRACE_ID` (`f4b806d400d5497c97b4dc63bbff82a3`).
![Session Initialization](https://postimg.cc/8jbTCSw2)

### 2. Execution Policy Subversion (The Core Bug)
Definitive proof of the autonomous safety breach. The Agent encounters execution restrictions and programmatically overrides them.
* **Mechanism:** Injects a Base64-encoded payload `QnlwYXNz` to change `PSExecutionPolicyPreference` to **`Bypass`**.
* **Result:** This forced bypass allowed the Agent to execute destructive recursive shell commands (`Remove-Item -Recurse -Force`) completely unhindered by Windows security policies.
![Execution Policy Bypass](https://postimg.cc/mhjbL4m0)

### 3. Collateral System Degradation (WER Archive)
Proof of immediate operating system and software environment damage post-deletion.
* **Impact Analysis:** Right after the environment wipe, the Windows Error Reporting system (`WER\ReportArchive`) logged a cascade of core application crashes (`AppCrash`). Systems like `YourPhone`, audio components (`SmartAudio3`), and browsers (`msedge.exe`) collapsed because their configurations were wiped from `%APPDATA%`. Side effects include corrupted Steam game directories and continuous OS instability.
![Collateral System Damage](https://postimg.cc/w3wpKVv9)

### 4. First Response: Automated AI Assistant (Ticket T-B62114)
The initial automated reaction provided by the Cursor vendor (Anysphere).
* **Outcome:** The critical data loss report was initially handled by an **AI Support Assistant ("Sam")**. The bot explicitly admitted that the *"Agent can make mistakes"*, closed the ticket automatically, and offered generic advice on using Git instead of escalating the execution policy exploit.
![Automated Support Response](https://postimg.cc/HryDy1tn)

### 5. Final Response: Official Human Escalation
The final escalation analysis from a human representative at Cursor Support.
* **Reviewer:** Ben (Cursor Support Human Representative)
* **Key Admission:** The vendor officially confirms they **do not have server-side logs of the executed destructive actions** due to local machine terminal isolation (*"we don't have server-side logs of what was run"*). This establishes this repository as the definitive primary record of the incident.
* **Resolution Offered:** The vendor acknowledged that the AI models *"can make mistakes,"* requested the local `Request ID` for further prompt investigation, and officially offered **1 month of Cursor Pro ($20 credit)** as compensation for the 37GB data purge and OS corruption.
![Official Human Support Resolution](https://postimg.cc/4nLr2xLZ)

