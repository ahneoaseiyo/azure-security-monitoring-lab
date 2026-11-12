# Security Monitoring Assessment

## Objective

Determine whether subscription-level Azure Activity Logs can be collected and queried to detect controlled Azure RBAC permission changes.

## Method

- Forward subscription **Administrative** Activity Logs to a Log Analytics workspace through a diagnostic setting.
- Verify an administrative resource-group tag update is ingested into `AzureActivity`.
- Use an existing user-assigned managed identity as the test principal.
- Temporarily create a **Reader** role assignment scoped to the monitoring resource group.
- Delete that exact temporary role assignment.
- Query `AzureActivity` for successful `roleAssignments/write` and `roleAssignments/delete` operations.

## Observations — 12 November 2026

| Event | Observed UTC timestamp | Verification |
| --- | --- | --- |
| Diagnostic setting creation | 12:51 | Subscription Activity Log and configuration inspection |
| Resource group tag update | 13:35:43 | `AzureActivity` KQL results |
| Resource group event ingestion | 13:44:57 | `ingestion_time()` query |
| Temporary Reader assignment | 14:01:09 | Subscription Activity Log and `AzureActivity` query |
| Temporary Reader assignment deletion | 14:03:54 | Subscription Activity Log and `AzureActivity` query |

**Outcome:** The final manually executed KQL query returned two successful RBAC change records labeled **Role Assigned** and **Role Removed**.

## Security interpretation

A successful `roleAssignments/write` event is a useful signal for potential privilege escalation or unauthorized access changes; a successful `roleAssignments/delete` event indicates access removal. Neither event alone establishes malicious activity.

In this lab both events were explicitly authorized. The assigned permission was Reader at resource-group scope. The original managed identity's separate Reader assignment in another lab resource group was not targeted.

The logged caller reflects the identity making the assignment; it does not by itself explain which permissions were granted. A production investigation should inspect the target principal, role definition, assignment scope, change approval, and surrounding context.

## Validation status

| Claim | Assessment |
| --- | --- |
| Administrative events generated | Verified |
| Subscription diagnostics route to intended workspace | Configured and verified |
| Administrative event records ingested | Verified |
| Successful RBAC assignment detected manually | Verified |
| Successful RBAC removal detected manually | Verified |
| Temporary Reader assignment cleanup | User confirmed; deletion event independently verified |
| Unauthorized change detection | Not tested |
| Scheduled queries, alerts, notifications | Not implemented |
| Microsoft Sentinel integration | Not implemented |

## Limitations and improvements

1. **Manual detection only:** There is no scheduled alert rule. A future phase could add an Azure Monitor alert with defined thresholds and notification routing, subject to budget and permissions.
2. **Limited event coverage:** Only controlled role assignment and resource group changes were tested. No broader threat scenarios, authentication logs, or workload telemetry were collected.
3. **Investigative context:** The simple query displays caller, status, resource ID, and correlation ID. Additional fields or resource lookups would be needed to reliably identify role, grantee, and scope.
4. **Ingestion latency:** The tag update reached Log Analytics approximately nine minutes after its event timestamp. Collection latency can vary and matters for alert timeliness.
5. **Cost/availability trade-off:** A 0.1 GB/day workspace cap was configured for the student lab; it may interrupt collection if reached, and is not a total spending limit.

## Responsible lab operation

- Temporary test access was limited to Reader on a lab resource group.
- The temporary role assignment was removed.
- Public evidence should exclude personal identifiers, full subscription identifiers, and unnecessary internal metadata.
