# Evidence checklist

Add **sanitized** screenshots from your own Azure portal session if you want visual proof alongside the written results:

1. **Diagnostic setting** — Administrative category enabled, destination set to the lab workspace.
2. **Workspace KQL ingestion** — `AzureActivity` showing the post-configuration resource-group events.
3. **RBAC detection** — final query showing both `Role Assigned` and `Role Removed`.
4. **Cleanup** — an Activity Log entry for the successful role assignment deletion.

Suggested filenames:
- `01-diagnostic-setting-redacted.png`
- `02-azureactivity-ingestion-redacted.png`
- `03-rbac-detection-redacted.png`
- `04-rbac-cleanup-redacted.png`

Do **not** upload screenshots unreviewed. Remove account email addresses, subscription and tenant GUIDs, and any information you do not intend to disclose publicly. This directory currently contains only the checklist; no screenshot evidence is included.
