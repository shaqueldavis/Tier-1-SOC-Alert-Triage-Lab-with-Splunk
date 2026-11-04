# Tier 1 SOC Alert Triage Lab with Splunk

## Overview

This project documents a hands-on, end-to-end Tier 1 Security Operations Center (SOC) alert detection, investigation, and escalation workflow using Splunk Enterprise. It demonstrates how a SOC analyst can work with Windows endpoint telemetry, use a SIEM to identify suspicious authentication activity, investigate an alert, establish its scope, document findings, and make a defensible escalation decision.

The lab uses a small virtual environment with a Windows 11 endpoint forwarding event logs to a Splunk Enterprise server running on Ubuntu Server. After validating the log pipeline, the workflow moves from detection engineering into an analyst-style investigation of repeated failed logon events.

> **Lab context:** The failed logons used to validate the detection were intentionally generated in the lab. The investigation phases then approach the resulting alert from the perspective of a Tier 1 analyst who must assess the available telemetry without assuming the cause or outcome.

## Objective

Build and document a functional Splunk-based monitoring and alerting workflow, then apply a repeatable Tier 1 SOC process to a Windows authentication alert. The primary objectives are to:

- Deploy Splunk Enterprise and a Windows Universal Forwarder in separate VMware virtual machines.
- Collect Windows Security, Sysmon, and PowerShell Operational event logs.
- Verify that endpoint telemetry is successfully forwarded, indexed, and searchable in Splunk.
- Develop and schedule a Splunk Search Processing Language (SPL) detection for repeated failed logon attempts.
- Validate that the alert triggers when the detection criteria are met.
- Investigate the alert using Windows event data, SPL searches, and extracted XML fields.
- Establish the scope of the activity, assess related authentication events, and document an evidence-based disposition.
- Demonstrate how a Tier 1 analyst records findings and escalates an alert when the available evidence is inconclusive.

## Project Results

The completed lab demonstrates the ability to:

- Build a two-VM SIEM lab using VMware Workstation Pro, Windows 11, and Ubuntu Server.
- Install and configure Splunk Enterprise and the Splunk Universal Forwarder.
- Configure the forwarder to collect Windows Security, Sysmon, and PowerShell Operational logs in XML format.
- Troubleshoot a missing Sysmon data source by identifying the Universal Forwarder's service account and granting it membership in the Windows **Event Log Readers** group.
- Confirm ingestion and searchability of the configured event sources in Splunk.
- Create SPL logic that identifies **five or more Windows failed logon events (Event ID 4625) within a 10-minute window**.
- Configure a scheduled Splunk alert, review its triggered result, and validate the alert through controlled event generation.
- Investigate the alert by examining the host, event ID, event timestamps, target-account fields, source IP address, logon type, failure reason, status, and substatus.
- Search for related successful logons (Event ID 4624) and evaluate whether the available evidence supports a relationship to the failed attempts.
- Document the investigation in a Jira ticket, identify a potential MITRE ATT&CK mapping, and recommend escalation when the activity cannot be confidently classified as benign or malicious.

### Detection scenario and investigation outcome

The detection is named **Repeat Failed Login Attempts**. Its SPL search groups Windows Security Event ID 4625 records into 10-minute windows and returns results when five or more failures are observed for a host.

During the documented investigation, the five failed logon events showed a local loopback source address (127.0.0.1) and Logon Type 2 (Interactive). The failed events did not identify a target username (TargetUserName was not populated and TargetUserSid was S-1-0-0). The investigation also reviewed nearby successful Event ID 4624 activity. Although successful logons for a Microsoft account occurred shortly after the failures, the missing target-account information in the failed events prevented a conclusive correlation.

The event data included status 0xC000006D (STATUS_LOGON_FAILURE) and substatus 0xC0000380 (STATUS_SMARTCARD_WRONG_PIN). The substatus describes a smart-card PIN error code; it does **not**, by itself, establish that a smart card was used. The available telemetry did not establish malicious activity, but it was also insufficient to confidently determine that the activity was benign. The documented Tier 1 disposition is therefore **undetermined — escalate for further investigation**, retaining the Medium severity.

A potential MITRE ATT&CK reference documented in the ticket is **T1110 – Brute Force**. This is a possible technique mapping for the repeated authentication failures, not a conclusion that a brute-force attack was confirmed.

## Lab Environment

The lab is built with two virtual machines connected through VMware NAT:

| Component | Role |
|---|---|
| Windows 11 virtual machine | Endpoint generating Windows Security, Sysmon, and PowerShell event telemetry |
| Ubuntu Server virtual machine | Hosts Splunk Enterprise and receives forwarded event data |
| Splunk Universal Forwarder | Collects selected Windows event logs and forwards them to Splunk Enterprise |
| Splunk Enterprise | Central SIEM used to search, detect, alert, and investigate |
| VMware Workstation Pro | Hosts and isolates the virtual lab environment |

The setup guides specify the virtual machine configuration and walk through installation and connectivity. VMware Workstation Pro must already be installed on the host computer. A computer capable of running both virtual machines and internet access are also required.

## Skills Demonstrated

- **SIEM Deployment and Configuration** — Installed Splunk Enterprise on Ubuntu Server and configured it to receive forwarded data from a Windows endpoint.
- **Log Collection and Ingestion** — Configured Splunk Universal Forwarder inputs for Windows Security, Sysmon Operational, and PowerShell Operational event logs.
- **Log Source and Ingestion Validation** — Used PowerShell, Splunk Search & Reporting, and SPL to verify event availability, host attribution, sources, and sourcetypes.
- **Troubleshooting** — Investigated missing Sysmon telemetry, identified the Universal Forwarder service identity, and corrected event-log read permissions.
- **SPL Search Development** — Used search filters, time bucketing, aggregation, conditional thresholds, field extraction with rex, and result formatting to investigate Windows event data.
- **Detection Engineering** — Developed and tested a threshold-based detection for repeated Windows failed logons and configured it as a scheduled Splunk alert.
- **Alert Monitoring and Validation** — Reviewed Triggered Alerts and verified that the detection fired after generating controlled failed logon events.
- **Alert Investigation and Triage** — Examined Event ID 4625 details, including account identifiers, source IP, logon type, failure reason, status, and substatus.
- **Scope and Correlation Analysis** — Reviewed Event ID 4624 activity, compared timestamps and available authentication attributes, and documented the limits of event correlation.
- **Incident Documentation and Escalation** — Prepared investigation notes in Jira, assessed severity and disposition, and documented the rationale for Tier 2 escalation.
- **MITRE ATT&CK Familiarity** — Identified T1110 (Brute Force) as a potential mapping for the observed repeated failed authentication activity.

## Tools and Technologies

| Tool / Technology | Purpose in the Project |
|---|---|
| VMware Workstation Pro | Created and ran the isolated Windows and Ubuntu virtual machines using VMware NAT networking. |
| Windows 11 | Served as the monitored endpoint and source of authentication and system telemetry. |
| Ubuntu Server 24.04 LTS | Hosted the Splunk Enterprise instance. |
| Splunk Enterprise | SIEM platform used for data ingestion, SPL searches, detection, scheduled alerting, and investigation. |
| Splunk Universal Forwarder | Collected selected Windows Event Logs and forwarded them to Splunk Enterprise over TCP 9997. |
| Sysmon | Provided additional Windows endpoint telemetry through the Sysmon Operational event channel. |
| Windows Event Viewer | Used to inspect and validate locally generated Windows event records. |
| PowerShell | Used to generate and inspect Windows events, configure inputs and permissions, manage services, and test connectivity. |
| SPL (Search Processing Language) | Used to search, filter, aggregate, extract, and correlate event data and build the detection. |
| SSH | Provided remote command-line access from the Windows VM to the Ubuntu Splunk server. |
| UFW | Allowed the Splunk receiving port (TCP 9997) on the Ubuntu server. |
| Jira | Used to document the alert investigation, findings, severity, disposition, and escalation. |
| MITRE ATT&CK | Referenced to document a potential technique mapping (T1110 – Brute Force). |

## Lab Phases

Each Markdown file in 01_Setup and 02_Operational Steps represents an individual phase. Follow the phases in order.

### Part 1 — Environment Setup and Log Ingestion

| Phase | Guide | What was completed |
|---|---|---|
| 1 | [Install Windows Client](01_Setup/01.%20Install%20Windows%20Client.md) | Created and configured the Windows 11 virtual machine that serves as the monitored endpoint. |
| 2 | [Install Linux Server](01_Setup/02.%20Install%20Linux%20Server.md) | Installed and updated Ubuntu Server, configured the VM, enabled SSH access, and prepared the Splunk server environment. |
| 3 | [Download Splunk to Linux](01_Setup/03.%20Download%20Splunk%20to%20Linux.md) | Downloaded and installed the Splunk Enterprise package on Ubuntu Server. |
| 4 | [Download Splunk to Windows](01_Setup/04.%20Download%20Splunk%20to%20Windows.md) | Downloaded and installed the Splunk Universal Forwarder on the Windows endpoint and configured its receiving server destination. |
| 5 | [Download Sysmon](01_Setup/05.%20Download%20Sysmon.md) | Installed Sysmon on the Windows VM and verified that Sysmon Operational events were being generated. |
| 6 | [Configure Splunk Enterprise](01_Setup/06.%20Configure%20Splunk%20Enterprise.md) | Started Splunk Enterprise, accessed the web interface, configured the receiving listener on TCP 9997, and addressed hostname resolution and connectivity. |
| 7 | [Set Up Log Ingestion](01_Setup/07.%20Set%20Up%20Log%20Ingestion.md) | Configured the Universal Forwarder's inputs.conf to collect Windows Security, Sysmon Operational, and PowerShell Operational logs in XML format; validated the configuration and restarted the service. |
| 8 | [Verify Logs Reach Dashboard](01_Setup/08.%20Varify%20Logs%20Reach%20Dashboard.md) | Confirmed that endpoint events were searchable in Splunk, checked host and sourcetype values, and troubleshot missing Sysmon events by correcting Event Log Readers permissions. |

### Part 2 — Detection, Alerting, and SOC Investigation

| Phase | Guide | What was completed |
|---|---|---|
| 9 | [Generate Events](02_Operational%20Steps/01.%20Generate%20Events.md) | Generated controlled failed Windows logons, verified Event ID 4625 locally, and developed SPL to identify five or more failures in a 10-minute window. |
| 10 | [Create Automated Alert](02_Operational%20Steps/02.%20Create%20Automated%20Alert.md) | Saved the detection as a scheduled Splunk alert named **Repeat Failed Login Attempts**, configured its schedule, time window, throttling, and Medium severity. |
| 11 | [Test Alert](02_Operational%20Steps/03.%20Test%20Alert.md) | Repeated the controlled failed-logon scenario and verified that the alert appeared in Splunk's Triggered Alerts. |
| 12 | [Investigate & Triage Alert](02_Operational%20Steps/04.%20Investigate%20%26%20Triage%20Alert.md) | Followed a structured Tier 1 process to capture the alert details, validate the detection, and prepare the investigation. |
| 13 | [Establish Scope](02_Operational%20Steps/05.%20Establish%20Scope.md) | Investigated the target-account fields, authentication source, logon type, failure reason, status/substatus codes, and related successful logons. |
| 14 | [Finalize Ticket](02_Operational%20Steps/06-Finalize-Ticket.md) | Assessed malicious versus benign activity, documented findings in Jira, recorded a potential MITRE ATT&CK mapping, and recommended escalation because the evidence was inconclusive. |

## Detection Logic

The final detection search used in the lab is:

~~~spl
index=* sourcetype="XmlWinEventLog:Security" "<EventID>4625</EventID>"
| bin _time span=10m
| stats count as failed_logins by _time host
| where failed_logins >= 5
~~~

This search:
1. Searches Windows Security XML events for Event ID 4625 (failed logon).
2. Groups matching events into 10-minute time windows.
3. Counts failed logons by time window and host.
4. Returns results when the count reaches five or more.

The scheduled alert is configured to run every five minutes, search the preceding 10 minutes, and suppress repeat triggering for the same result for 10 minutes. Its trigger action adds the result to Triggered Alerts with Medium severity.

## Repository Structure

~~~text
Tier-1-SOC-Alert-Triage-Lab-with-Splunk/
├── 01_Setup/
│   ├── 01. Install Windows Client.md
│   ├── 02. Install Linux Server.md
│   ├── 03. Download Splunk to Linux.md
│   ├── 04. Download Splunk to Windows.md
│   ├── 05. Download Sysmon.md
│   ├── 06. Configure Splunk Enterprise.md
│   ├── 07. Set Up Log Ingestion.md
│   └── 08. Varify Logs Reach Dashboard.md
├── 02_Operational Steps/
│   ├── 01. Generate Events.md
│   ├── 02. Create Automated Alert.md
│   ├── 03. Test Alert.md
│   ├── 04. Investigate & Triage Alert.md
│   ├── 05. Establish Scope.md
│   └── 06-Finalize-Ticket.md
└── README.md
~~~

## Portfolio Focus

This repository is intended to demonstrate practical familiarity with Splunk in a Tier 1 SOC workflow—not simply the installation of a SIEM. It documents the progression from endpoint telemetry collection and search development through alert validation, evidence-based investigation, ticket documentation, and escalation.

The phase guides include the operational steps, commands, SPL searches, troubleshooting notes, and supporting screenshots used throughout the lab.
