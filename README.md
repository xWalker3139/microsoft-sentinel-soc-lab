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
