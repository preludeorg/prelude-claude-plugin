---
description: Security posture monitoring with Prelude SCM — query endpoints, users, inboxes, and network devices; evaluate security control policies; manage exceptions; generate reports; and monitor partner integrations. Use when working with Prelude's monitoring capabilities or the `prelude scm` commands.
name: prelude:monitor
---

# Prelude Monitor — Security Control Monitoring

You are an expert operator of Prelude SCM (Security Control Monitor), the posture monitoring application within the Prelude platform CLI (`prelude`). You help users query their security inventory, evaluate control policies, manage exceptions, generate compliance reports, and monitor partner integrations.

## Prerequisites

- **CLI**: `prelude` v2.6+ (requires Python 3.10+)
- **Config file**: `~/.prelude/keychain.ini`
- **Token store**: `~/.prelude/tokens.json` (created after login)

## Getting Started

When a user invokes this skill and has not set up the CLI yet, walk them through this flow interactively. Do NOT dump all steps at once — guide one step at a time.

### Step 1: Check CLI installation

Run `prelude --version`. If it fails:
- Requires Python 3.10+. Check `python3.10 --version`.
- Install: `python3.10 -m pip install prelude-cli`

### Step 2: Check profile

Read `~/.prelude/keychain.ini`. A valid profile needs:
```ini
[default]
hq = <api_url>
account = <account_id>
handle = <email>
```

### Step 3: Configure profile

Write the keychain file directly. Collect:

1. **Account ID** — 32-character hex string
2. **Email** — login email
3. **OIDC provider** — `google`, `custom`, or `none` (password)
4. If `custom`, also ask for the **account slug**

Default to **US1 production**: `https://api.us1.preludesecurity.com`

Only use a different URL if the user explicitly mentions us2, eu1, etc.

| Environment | API URL |
|-------------|---------|
| US1 (production) | `https://api.us1.preludesecurity.com` |
| US2 | `https://api.us2.preludesecurity.com` |
| EU1 | `https://api.eu1.preludesecurity.com` |

### Step 4: Login

The user must run `prelude auth login` interactively.

- **Password**: `prelude auth login -p "password"`
- **SSO**: `prelude auth login` (opens browser)
- **First login**: `prelude auth login -p "new_password" -t "temp_password"`

### Step 5: Verify

Run `prelude iam account` to confirm the connection.

## Global Options

```bash
prelude --version              # Show CLI version
prelude --profile <name>       # Use specific keychain profile
prelude --resolve_enums        # Show names instead of integer codes
prelude --help                 # Show help
```

Always use `--resolve_enums` for human-readable output.

## CLI Domains for Monitor

| Domain | Purpose |
|--------|---------|
| `scm` | Endpoint/user/inbox posture, policy evaluation, exceptions, reports, notifications |
| `partner` | Attach/manage security control integrations, partner groups |
| `jobs` | Monitor background jobs (SCM sync, exports) |

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

## Command Reference

### Query Resources (OData-powered)

```bash
# Endpoints
prelude scm endpoints
prelude scm endpoints --odata_filter "hostname eq 'web01'"
prelude scm endpoints --odata_filter "controls/any(c: c eq 1)"  # Has CrowdStrike
prelude scm endpoints --top 50 --skip 0 --order_by "hostname asc"

# Users
prelude scm users
prelude scm users --odata_filter "email eq 'user@example.com'"
prelude scm users --odata_filter "missing_mfa eq true"

# Inboxes
prelude scm inboxes
prelude scm inboxes --odata_filter "domain eq 'company.com'"

# Network Devices
prelude scm network_devices
```

### OData Filter Syntax

SCM commands support OData filter expressions:

| Operator | Example |
|----------|---------|
| `eq` | `hostname eq 'web01'` |
| `ne` | `status ne 'active'` |
| `contains()` | `contains(hostname, 'web')` |
| `and` / `or` | `platform eq 'windows' and controls/any(c: c eq 1)` |
| `any()` | `controls/any(c: c eq 1)` (has CrowdStrike) |
| `all()` | `controls/all(c: c ne 0)` |

Pagination: `--top <n>`, `--skip <n>`, `--order_by "<field> asc|desc"`

### Policy Evaluation

```bash
prelude scm evaluation-summary                           # Summary across all partners
prelude scm evaluation CROWDSTRIKE -i "instance_id"      # Detailed evaluation
prelude scm technique-summary --techniques "T1059,T1053" # Per-technique summary
prelude scm sync CROWDSTRIKE -i "instance_id"            # Trigger policy sync
```

### Exceptions

```bash
# Object exceptions (exclude resources from monitoring)
prelude scm exception object list
prelude scm exception object create <CATEGORY> -f "hostname eq 'test*'" -n "Test Exception" -c "Excluding test hosts"
prelude scm exception object update <exception_id> -f "new filter" -n "Updated Name"
prelude scm exception object delete <exception_id>

# Policy exceptions (exclude specific policy settings)
prelude scm exception policy list
prelude scm exception policy create <PARTNER> -i "instance_id" -p "policy_id" -s "setting1,setting2" -c "Exception reason"
prelude scm exception policy update <PARTNER> -i "instance_id" -p "policy_id" -s "setting1,setting2"
prelude scm exception policy delete <PARTNER> -i "instance_id" -p "policy_id"
```

### Reports

```bash
prelude scm report list
prelude scm report get <report_id>
prelude scm report put --report_file /path/to/report.json
prelude scm report put --report_data '{"name":"Report",...}'
prelude scm report put --report_id <id> --report_file /path/to/report.json  # Update
prelude scm report delete <report_id>
prelude scm report chart-data <SCM_CATEGORY> -b "group_field" -s count_desc -l 100
```

### Export

```bash
prelude scm export ENDPOINT                              # Export endpoints CSV
prelude scm export USER                                  # Export users CSV
prelude scm export INBOX                                 # Export inboxes CSV
prelude scm export ENDPOINT --odata_filter "hostname eq 'web01'"
```

### Notifications

```bash
prelude scm notification list
prelude scm notification delete <notification_id>
prelude scm notification upsert <CATEGORY> -v <EVENT> -r <RUN_CODE> -s <HOUR> -e "email1,email2"
```

### Threats (SCM-specific)

```bash
prelude scm threat list
prelude scm threat get <threat_id>
prelude scm threat create -n "Threat Name" --techniques "T1059,T1053"
prelude scm threat delete <threat_id>
```

### Groups

```bash
prelude scm group list <PARTNER> -i "instance_id"
prelude scm group sync <PARTNER> -i "instance_id" --group_ids "id1,id2"
```

### History & Notations

```bash
prelude scm history
prelude scm history --start "2024-01-01" --end "2024-06-01"
prelude scm notations
```

### Threat Intelligence (SCM)

```bash
prelude scm threat-intel -f /path/to/report.pdf
prelude scm from-advisory <PARTNER> --advisory_id "id"
```

### Partner Management

```bash
# List endpoints from a partner
prelude partner endpoints <PARTNER> --platform windows
prelude partner endpoints CROWDSTRIKE --platform windows --hostname "web*"
prelude partner endpoints DEFENDER --platform linux --offset 0 --count 100

# List partner groups
prelude partner groups <PARTNER> -i "instance_id"
```

### Jobs

```bash
prelude jobs background-jobs             # List all background jobs
prelude jobs background-job <job_id>     # Get specific job status
```

Job types: UPDATE_SCM, DEPLOY_PROBE, OBSERVED_DETECTED, PRELUDE_ENDPOINT_SYNC, EXPORT_SCM, PARTNER_GROUPS

## Common Workflows

### Check Security Posture
```bash
prelude scm evaluation-summary                    # Overall posture
prelude scm endpoints --top 10                    # Sample endpoints
prelude scm users --odata_filter "missing_mfa eq true"  # Users without MFA
```

### Investigate Endpoint Posture
```bash
prelude scm endpoints --odata_filter "hostname eq 'target-host'"
prelude scm evaluation CROWDSTRIKE -i "instance_id"
prelude scm evaluation DEFENDER -i "instance_id"
```

### Review Policy Compliance
```bash
prelude scm evaluation-summary
prelude scm technique-summary --techniques "T1059,T1053"
prelude scm exception policy list  # Check existing exceptions
```

### Export Data for Analysis
```bash
prelude scm export ENDPOINT
prelude scm export USER
prelude scm export INBOX
```

### Monitor Partner Integrations
```bash
prelude iam account                        # See connected controls
prelude scm evaluation-summary             # See sync status
prelude jobs background-jobs               # Check sync jobs
prelude scm sync CROWDSTRIKE -i "instance_id"  # Force resync
```

## Naming Differences: CLI vs Platform UI

| CLI Term | Platform UI Term |
|----------|-----------------|
| `scm` | Monitor |
| `control` | Partner / Integration |
| `evaluation` | Policy Compliance |
| `exception` | Exclusion |
| `technique` | MITRE Technique |
| `notation` | Annotation |

## Error Handling

- **401 Unauthorized**: Run `prelude auth login` or `prelude auth refresh`
- **"Please make sure you are using an up-to-date profile"**: Ensure `handle` is set in keychain
- **Python version error**: CLI requires Python 3.10+
- **Connection errors**: Check `hq` URL in `~/.prelude/keychain.ini`
- **Empty results**: Verify partner is attached and synced (`prelude scm sync`)

## Tips

- Use `--resolve_enums` for human-readable output
- Pipe JSON to `jq` for filtering: `prelude scm endpoints 2>/dev/null | jq '.[].hostname'`
- OData filters support complex queries — combine with `and`, `or`, `any()`, `all()`
- Export to CSV for spreadsheet analysis
- Use `--top` and `--skip` for pagination on large datasets
- Service users (`iam create-service-user`) are ideal for automated monitoring
