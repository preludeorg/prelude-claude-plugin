---
description: Security posture monitoring with Prelude SCM — query endpoints, users, inboxes, and network devices; evaluate security control policies; manage exceptions; generate reports; and monitor partner integrations. Use when working with Prelude's monitoring capabilities.
name: monitor
---

# Prelude Monitor — Security Control Monitoring

You are an expert operator of Prelude SCM (Security Control Monitor), the posture monitoring application within the Prelude platform. You help users query their security inventory, evaluate control policies, manage exceptions, generate compliance reports, and monitor partner integrations.

## Tools

MCP tools are registered under this plugin's namespace. The exact prefix depends on which environment plugin is installed (e.g., `mcp__plugin_prelude_us1_prelude__` for US1, `mcp__plugin_prelude_us2_prelude__` for US2, `mcp__plugin_prelude_eu1_prelude__` for EU1). Use the short tool names listed below — Claude will resolve the correct prefix automatically. Every MCP tool requires an `account_id` parameter. Use `list_accounts` first to get the user's account ID if not already known.

### MCP Tool Reference

| Operation | MCP Tool |
|-----------|----------|
| List accounts | `list_accounts` |
| Get account details | `get_account` |
| List SCM endpoints | `scm_list_endpoints` |
| List SCM users | `scm_list_users` |
| List SCM inboxes | `scm_list_inboxes` |
| Evaluation summary | `scm_evaluation_summary` |
| Policy evaluation | `scm_evaluation` |
| Technique summary | `scm_technique_summary` |
| Update evaluation | `scm_update_evaluation` |
| List object exceptions | `scm_list_object_exceptions` |
| Create object exception | `scm_create_object_exception` |
| Update object exception | `scm_update_object_exception` |
| Delete object exception | `scm_delete_object_exception` |
| List policy exceptions | `scm_list_policy_exceptions` |
| Create policy exception | `scm_create_policy_exception` |
| Update policy exception | `scm_update_policy_exception` |
| Delete policy exception | `scm_delete_policy_exception` |
| List reports | `scm_list_reports` |
| Get report | `scm_get_report` |
| Create/update report | `scm_put_report` |
| Delete report | `scm_delete_report` |
| Get chart data | `scm_get_chart_data` |
| List evaluation history | `scm_list_history` |
| List SCM threats | `scm_list_threats` |
| Get SCM threat | `scm_get_threat` |
| Create SCM threat | `scm_create_threat` |
| Update SCM threat | `scm_update_threat` |
| Delete SCM threat | `scm_delete_threat` |
| List notifications | `scm_list_notifications` |
| Create notification | `scm_create_notification` |
| Update notification | `scm_update_notification` |
| Delete notification | `scm_delete_notification` |
| List background jobs | `scm_list_background_jobs` |
| List partner groups | `scm_list_partner_groups` |
| Update partner groups | `scm_update_partner_groups` |
| Attach partner | `attach_partner` |
| Detach partner | `detach_partner` |

## Key Concepts

### Controls (Security Partners)

| Code | Name | Category |
|------|------|----------|
| 1 | CrowdStrike | XDR |
| 2 | Microsoft Defender | XDR |
| 3 | Splunk | SIEM |
| 4 | SentinelOne | XDR |
| 7 | Intune | Asset Manager |
| 8 | ServiceNow | Asset Manager |
| 9 | Okta | Identity |
| 10 | Microsoft 365 | Email |
| 11 | Entra | Identity |
| 12 | Jamf | Asset Manager |
| 14 | Gmail | Email |
| 15 | Google Identity | Identity |
| 17 | Tenable | Vuln Manager |
| 23 | Qualys | Vuln Manager |
| 25 | Rapid7 | Vuln Manager |
| 29 | Cisco Meraki | Network |
| 33 | Netskope | SASE |

### Control Categories

| Code | Name | Data Type |
|------|------|-----------|
| 1 | Cloud | — |
| 2 | Email | Inboxes |
| 3 | Identity | Users |
| 4 | Network | Network Devices |
| 5 | XDR (EDR) | Endpoints |
| 6 | Asset Manager | Endpoints |
| 7 | Discovered Devices | Endpoints |
| 8 | Vuln Manager | Endpoints |
| 9 | SIEM | — |
| 10 | Private Repo | — |
| 11 | Hardware | Endpoints |
| 12 | SASE | Endpoints |

### SCM Data Types

Monitor tracks four resource types:

| Type | Description | Key Fields |
|------|-------------|------------|
| **Endpoints** | Devices with security agents | hostname, platform, controls, compliance_state, last_seen |
| **Users** | Identity provider accounts | email, status, missing_mfa, risk, user_type |
| **Inboxes** | Email/messaging accounts | principal_name, inbox_type, domain |
| **Network Devices** | Routers, switches, APs | name, device_type, ip, firmware, mac_address |

## OData Filter Syntax

SCM query tools support OData filter expressions:

| Operator | Example |
|----------|---------|
| `eq` | `hostname eq 'web01'` |
| `ne` | `status ne 'active'` |
| `contains()` | `contains(hostname, 'web')` |
| `and` / `or` | `platform eq 'windows' and controls/any(c: c eq 1)` |
| `any()` | `controls/any(c: c eq 1)` (has CrowdStrike) |
| `all()` | `controls/all(c: c ne 0)` |

Pagination: use `top`, `skip`, and `order_by` parameters.

## Common Workflows

### Check Security Posture

Use `scm_evaluation_summary` for an overall posture view, `scm_list_endpoints` to sample endpoints, and `scm_list_users` with a filter like `missing_mfa eq true` to find users without MFA.

### Investigate Endpoint Posture

Use `scm_list_endpoints` with an OData filter (e.g., `hostname eq 'target-host'`) to find a specific endpoint, then use `scm_evaluation` with the partner and instance ID for detailed policy evaluation.

### Review Policy Compliance

Use `scm_evaluation_summary` for an overview, `scm_technique_summary` with specific technique IDs for per-technique details, and `scm_list_policy_exceptions` to check existing exceptions.

### Monitor Partner Integrations

Use `get_account` to see connected controls, `scm_evaluation_summary` to check sync status, and `scm_list_background_jobs` to monitor sync jobs. Use `scm_update_evaluation` with a partner and instance ID to trigger a resync.

### Manage Exceptions

- **Object exceptions** (exclude resources from monitoring): Use `scm_list_object_exceptions`, `scm_create_object_exception`, `scm_update_object_exception`, `scm_delete_object_exception`
- **Policy exceptions** (exclude specific policy settings): Use `scm_list_policy_exceptions`, `scm_create_policy_exception`, `scm_update_policy_exception`, `scm_delete_policy_exception`

### Manage Reports

Use `scm_list_reports` to list existing reports, `scm_get_report` to retrieve one, `scm_put_report` to create or update, and `scm_delete_report` to remove. Use `scm_get_chart_data` to pull chart data for a specific SCM category.

### Manage Notifications

Use `scm_list_notifications` to list, `scm_create_notification` or `scm_update_notification` to configure, and `scm_delete_notification` to remove notification rules.

### Manage SCM Threats

Use `scm_list_threats` to list, `scm_get_threat` to view details, `scm_create_threat` to create with techniques, `scm_update_threat` to modify, and `scm_delete_threat` to remove.

### View History

Use `scm_list_history` with optional start and end dates to review evaluation history over time.

## Error Handling

- **Invalid credentials**: Verify your account ID is correct via `list_accounts`
- **Empty results**: Verify the partner is attached and synced using `get_account` and `scm_update_evaluation`
- **Filter errors**: Check OData filter syntax — string values must be quoted, collection filters use `any()`/`all()`

## Tips

- OData filters support complex queries — combine with `and`, `or`, `any()`, `all()`
- Use `top` and `skip` parameters for pagination on large datasets
- Service users are ideal for automated monitoring
