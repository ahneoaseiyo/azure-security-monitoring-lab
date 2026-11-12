# Monitoring Architecture

```mermaid
flowchart TD
    subgraph Subscription["Azure for Students Subscription"]
      Activity["Azure Activity Log<br/>Administrative events"]
      Diag["Subscription Diagnostic Setting<br/>diag-security-monitoring-lab"]
      RG["Resource Group<br/>rg-security-monitoring-lab"]
      LAW["Log Analytics Workspace<br/>law-security-monitoring-lab"]
      Activity --> Diag
      Diag -->|"Administrative category"| LAW
      RG -. "Controlled role assignment tests" .-> Activity
    end
    LAW --> Table["AzureActivity table"]
    Table --> Investigation["KQL queries"]
    Investigation --> Review["Manual security investigation"]
```

## Data flow

1. Azure Resource Manager operations create subscription Activity Log entries.
2. The subscription diagnostic setting exports the **Administrative** category to the Log Analytics workspace.
3. Records arrive in the **AzureActivity** table after delivery and ingestion delays.
4. A user runs KQL queries manually to find role assignment writes and deletes.

## Boundary of the lab

- No virtual machine, agent, resource-level diagnostics, Microsoft Sentinel, or automated alerting.
- Only subscription-level Administrative Activity Logs were forwarded.
- This diagram represents the **verified logical configuration**, not a measured network path or a high-availability deployment.
