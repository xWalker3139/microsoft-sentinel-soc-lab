# Microsoft Sentinel SOC Detection & Incident Response Lab

## Overview

This project demonstrates the implementation of an end-to-end Security Operations Center (SOC) detection and incident response workflow using Microsoft Sentinel and Microsoft Entra ID.

The lab simulates an identity-related security event in which a user is added to a Microsoft Entra ID security group. The activity is ingested into Log Analytics, detected using a custom KQL query and Microsoft Sentinel analytics rule, and automatically converted into a security incident for investigation and triage.

The project covers the complete workflow:

**Identity Activity → Audit Logs → KQL Detection → Analytics Rule → Alert → Incident → Investigation → Triage → Closure**

## Technologies Used

- Microsoft Sentinel
- Microsoft Entra ID
- Azure Log Analytics
- Kusto Query Language (KQL)
- Microsoft Defender portal / Azure security tooling
- MITRE ATT&CK framework

## Lab Architecture

The detection pipeline implemented in this project is:

Microsoft Entra ID  
↓  
AuditLogs  
↓  
Log Analytics Workspace  
↓  
Microsoft Sentinel  
↓  
Custom KQL Analytics Rule  
↓  
Security Alert  
↓  
Sentinel Incident  
↓  
SOC Investigation & Triage

## Detection Scenario

The objective of the detection was to identify when a user is added to a Microsoft Entra ID group.

Group membership changes can be security-relevant because unauthorized modifications may provide users with access to resources or privileges they should not possess.

For this lab, the activity was intentionally generated in a controlled environment to validate the detection and incident response pipeline.

## KQL Detection

The following KQL query was used to identify group membership additions:

```kusto
AuditLogs
| where OperationName == "Add member to group"
| project TimeGenerated, OperationName, Result, InitiatedBy, TargetResources
| order by TimeGenerated desc

```

## Analytics Rule Configuration

A custom scheduled analytics rule named **User Added to Security Group** was created in Microsoft Sentinel.

The rule was configured with the following parameters:

- **Severity:** Medium
- **MITRE ATT&CK tactic:** Persistence
- **Query frequency:** Every 5 minutes
- **Lookup period:** Last 10 minutes
- **Alert threshold:** Greater than 0 results
- **Incident creation:** Enabled

This configuration allows Microsoft Sentinel to periodically query Entra ID audit logs and automatically generate an alert when a group membership addition is detected.

## Detection Validation

To validate the detection pipeline, a test user was intentionally added to a Microsoft Entra ID security group.

The generated activity appeared in the `AuditLogs` table with:

- `OperationName`: Add member to group
- `Result`: success
- `InitiatedBy`: Identity that performed the operation
- `TargetResources`: User and group affected by the operation

The successful query result confirmed that Entra ID audit activity was being ingested correctly into the Log Analytics workspace.

## Alert and Incident Generation

After the test activity was generated, the scheduled analytics rule detected the event and Microsoft Sentinel automatically created a **Medium severity** incident named:

**User Added to Security Group**

This demonstrated that the complete detection pipeline was functioning successfully:

**Entra ID Activity → AuditLogs → KQL → Analytics Rule → Alert → Incident**

## SOC Investigation

The generated incident was investigated using Microsoft Sentinel.

The investigation included:

1. Reviewing the alert associated with the incident.
2. Examining the underlying `AuditLogs` events.
3. Confirming that the operation completed successfully.
4. Reviewing the initiating identity.
5. Reviewing the affected user and security group.
6. Determining whether the activity represented authorized or malicious behavior.

The logs confirmed that the group membership modification was generated intentionally as part of the controlled SOC lab.

No malicious activity was identified.

## Incident Triage and Closure

Following investigation, the incident was:

- Assigned to an analyst.
- Documented with an investigation comment.
- Classified as **Benign Positive – Suspicious but expected**.
- Changed from **New** to **Closed**.

A second controlled group membership event also successfully triggered the analytics rule, demonstrating that the detection remained operational across repeated events.

## MITRE ATT&CK Mapping

The analytics rule was mapped to the **Persistence** tactic within the MITRE ATT&CK framework.

Group membership modifications can be security-relevant because an attacker with sufficient access may modify identity or group relationships to maintain access or obtain additional permissions.

> Note: The activity in this project was intentionally generated in a controlled lab environment and does not represent a real security compromise.

## Skills Demonstrated

This project demonstrates hands-on experience with:

- Microsoft Sentinel SIEM
- Microsoft Entra ID
- Azure Log Analytics
- Kusto Query Language (KQL)
- Custom analytics rule creation
- Identity-focused detection engineering
- Security alert investigation
- SOC incident triage
- Incident classification and closure
- MITRE ATT&CK mapping
- Security event validation and documentation

## Key Takeaways

This lab demonstrates how identity activity can be transformed from raw audit telemetry into an actionable SOC incident.

Rather than only querying security logs, the project implements the complete detection lifecycle: generating controlled activity, ingesting telemetry, developing detection logic, creating an analytics rule, generating an incident, investigating the underlying event, documenting the findings, and closing the incident after determining its disposition.

## Disclaimer

This project was created in a personal lab environment for cybersecurity education and portfolio demonstration purposes. All security events were intentionally generated and investigated in a controlled environment.
