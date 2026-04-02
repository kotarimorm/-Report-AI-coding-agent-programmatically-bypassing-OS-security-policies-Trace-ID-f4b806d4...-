NOT AN APRIL FOOLS' JOKE: I am the founder of GRAY_WHALE_CO , and I have documented a critical security flaw in Cursor AI Agent that bypasses OS policies and deletes system data. See logs below.

On March 26, 2026, while using the Cursor AI Agent for environment optimization and development (Project: oRcA_IDE), the Agent executed a series of destructive PowerShell commands that resulted in the loss of 37GB of data, including personal files, Python environments, and proprietary Assembly source code.

Technical Chain of Events (Verified via Event Viewer & PS History):

Environment Mapping (10:54 AM): The Agent initialized a session (Trace ID: f4b806d400d5497c97b4dc63bbff82a3) and performed a full dump of system environment variables (Dump-PowerShellState), mapping paths to %APPDATA%, %LOCALAPPDATA%, and project directories using Base64 encoding.

Security Policy Bypass (12:26 PM): After encountering an UnauthorizedAccess error while trying to run shellIntegration.ps1, the Agent programmatically bypassed the OS security policy.

Log Event 4104: Set-Item -LiteralPath 'Env:PSExecutionPolicyPreference' -Value 'Bypass' (Encoded in Base64 as QnlwYXNz).

Mass Deletion (12:27 PM): Immediately following the bypass, the Agent executed recursive deletion commands found in ConsoleHost_history.txt:

Remove-Item "c:\Users\HP\Desktop\test\*" -Recurse -Force -ErrorAction SilentlyContinue

rm -r WhaleBrowser -Force

System Collapse: Following the deletion, multiple system applications and dev tools (Blender, Python, 3DViewer) began to crash consistently. Windows Error Reporting (WER) shows Exception Code 80073db8 (Package integrity failure).

Support Response:
Upon reaching out to Cursor Support (Ticket T-B62114), the initial response was a template suggesting the use of Git for version control and Checkpoints for recovery.

Note: Git and Checkpoints do not cover global %APPDATA% folders, installed applications, or system-wide environment corruption caused by a rogue script.

After escalating the issue and providing Trace IDs, the final offer from the support team was one month of Cursor Pro ($20) as compensation for the loss of infrastructure and proprietary intellectual property.

Current Status:
The system remains partially corrupted (registry issues, broken runtime libraries). I am sharing this to warn other developers about the potential for unprompted "Execution Policy Bypass" by AI agents and the lack of robust safeguards when the Agent operates outside the immediate project scope. Screenshots, logs, and technical details here: https://postimg.cc/gallery/tqjRzSp
