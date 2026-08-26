# Tier 1 SOC Alert Triage Lab with Splunk

## Objective

Use Splunk to ingest Windows security and Sysmon events, investigate three alerts, classify each alert, document the evidence, close a false positive, and escalate a confirmed incident according to a written playbook.

## Project Results

The completed workflow successfully:

- RESULTS LISTED HERE

### Before You Begin

This lab begins with VMware Workstation Pro already installed on the host computer. 

To follow this lab, you will need:

- VMware Workstation Pro installed on your computer.
- A computer capable of running a Windows 11 virtual machine.
- Internet access.

### Skills Learned

* **SKILL HERE** - description of how skill was used and results.
* **Detection Engineering** - DESCRIPTION HERE.

### Tools and Technologies Used

| Tool / Technology | Purpose in the Project |
|---|---|
| PLACE HOLDER | PLACE HOLDER |

## Steps

### Phase 1. Repeated failed logins

Generate multiple Windows failed-logon events and create a Splunk search for Event ID 4625.

Investigate:

Targeted username
Source workstation or IP
Number and frequency of attempts
Logon type
Whether a successful login followed
Whether the activity matches expected testing

Final disposition: either suspicious password guessing or an approved test/false positive.

### Phase 2. Encoded PowerShell execution

Execute a harmless encoded PowerShell command inside your isolated Windows VM, such as one that only writes a test message.

Detect it through:

Sysmon Event ID 1
PowerShell Event ID 4104, if script-block logging is enabled
Command-line arguments containing -EncodedCommand

Map the behavior to MITRE ATT&CK and document why encoded PowerShell deserves investigation even when the demonstrated command is harmless.

Final disposition: controlled simulation, but describe how the same evidence would be escalated in production.

### Phase 3. Local administrator-group modification

Create a temporary test account and add it to the local Administrators group.

Investigate relevant Windows events, such as:

Account creation
Security-group membership change
Actor account
Target account
Affected endpoint
Whether the change was authorized

Final disposition: escalate as an unauthorized privilege change, then remove the test account as your containment step.

