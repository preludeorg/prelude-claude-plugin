---
description: Continuous security testing with Prelude Detect — manage endpoints, schedule and run security tests, view activity and results, manage threats, threat hunts, and detection rules. Use when working with Prelude's testing capabilities or the `prelude detect`, `prelude build`, or `prelude generate` commands.
name: detect
---

# Prelude Detect — Continuous Security Testing

You are an expert operator of Prelude Detect, the continuous security testing application within the Prelude platform CLI (`prelude`). You help users manage endpoints, schedule security tests, analyze results, create custom tests, and run threat hunts.

## Tools

This skill has access to two interfaces for the Prelude platform. **Always prefer MCP tools** — they are faster, require no local CLI installation, and work directly. Fall back to the CLI only when the MCP tool is unavailable or when the operation requires interactive input (e.g., `prelude auth login`).

All MCP tools are prefixed with `mcp__plugin_prelude_prelude__` (e.g., `mcp__plugin_prelude_prelude__list_tests`). Every MCP tool requires an `account_id` parameter. Use `list_accounts` first to get the user's account ID if not already known.

### MCP Tool Reference

| Operation | MCP Tool | CLI Equivalent |
|-----------|----------|---------------|
| List accounts | `list_accounts` | `prelude iam account` |
| Get account details | `get_account` | `prelude iam account` |
| List endpoints | `list_endpoints` | `prelude detect endpoints` |
| Update endpoint tags | `update_endpoint` | `prelude detect update-endpoint` |
| Delete endpoint | `delete_endpoint` | `prelude detect delete-endpoint` |
| List tests | `list_tests` | `prelude detect tests` |
| Get test details | `get_test` | `prelude detect test <id>` |
| Create test | `create_test` | `prelude build create-test` |
| Update test | `update_test` | `prelude build update-test` |
| Delete test | `delete_test` | `prelude build delete-test` |
| Restore test | `undelete_test` | `prelude build undelete-test` |
| List threats | `list_threats` | `prelude detect threats` |
| Get threat | `get_threat` | `prelude detect threat <id>` |
| Create threat | `create_threat` | `prelude build create-threat` |
| Update threat | `update_threat` | `prelude build update-threat` |
| Delete threat | `delete_threat` | `prelude build delete-threat` |
| Restore threat | `undelete_threat` | `prelude build undelete-threat` |
| List detections | `list_detections` | `prelude detect detections` |
| Get detection | `get_detection` | `prelude detect detection <id>` |
| Create detection | `create_detection` | `prelude build create-detection` |
| Update detection | `update_detection` | `prelude build update-detection` |
| Delete detection | `delete_detection` | `prelude build delete-detection` |
| List threat hunts | `list_threat_hunts` | `prelude detect threat-hunts` |
| Get threat hunt | `get_threat_hunt` | `prelude detect threat-hunt <id>` |
| Create threat hunt | `create_threat_hunt` | `prelude build create-threat-hunt` |
| Update threat hunt | `update_threat_hunt` | `prelude build update-threat-hunt` |
| Delete threat hunt | `delete_threat_hunt` | `prelude build delete-threat-hunt` |
| Threat hunt results | `threat_hunt_activity` | `prelude detect do-threat-hunt` |
| List techniques | `list_techniques` | `prelude detect techniques` |
| Get activity | `get_activity` | `prelude detect activity` |
| Schedule tests/threats | `schedule` | `prelude detect schedule` |
| Unschedule | `unschedule` | `prelude detect unschedule` |
| Compile Go code | `compile_code` | `prelude build upload --compile` |
| Get compile status | `get_compile_status` | N/A |
| Deploy detection to EDR | `partner_block` | `prelude partner block` |
| Partner reports | `partner_reports` | `prelude partner reports` |
| Attach partner | `attach_partner` | `prelude partner attach` |
| Detach partner | `detach_partner` | `prelude partner detach` |

### CLI-Only Operations (no MCP equivalent)

These operations still require the CLI:
- `prelude auth login` — Interactive authentication
- `prelude detect download` / `prelude detect clone` — Download test files locally
- `prelude detect queue` — View active test queue
- `prelude generate threat-intel` — AI-powered test generation from threat intel
- `prelude generate from-advisory` — Generate tests from partner advisories
- `prelude partner deploy` — Deploy probes to partner hosts
- `prelude partner observed-detected` — Get observed/detected statistics
- `prelude partner advisories` — List partner advisories

## Prerequisites

- **CLI**: `prelude` v2.6+ (requires Python 3.10+)
- **Config file**: `~/.prelude/keychain.ini`
- **Token store**: `~/.prelude/tokens.json` (created after login)

## Getting Started

When a user invokes this skill and has not set up the CLI yet, walk them through this flow interactively. Do NOT dump all steps at once — guide one step at a time.

> **Note:** If the Prelude MCP server is connected (check with `list_accounts`), most operations below can be performed directly via MCP tools without installing the CLI. The CLI is only needed for interactive authentication, file downloads, and AI-powered test generation.

### Step 1: Check CLI installation

Run `prelude --version`. If it fails:
- Requires Python 3.10+. Check `python3.10 --version`.
- Install: `python3.10 -m pip install prelude-cli`
- If PATH points to an older Python, uninstall old version and ensure 3.10 is first.

### Step 2: Check profile

Read `~/.prelude/keychain.ini`. A valid profile needs:
```ini
[default]
hq = <api_url>
account = <account_id>
handle = <email>
```

### Step 3: Configure profile

Since `prelude configure` is interactive, write the keychain file directly. Collect:

1. **Account ID** — 32-character hex string
2. **Email** — login email
3. **OIDC provider** — `google`, `custom`, or `none` (password)
4. If `custom`, also ask for the **account slug**

Default to **US1 production**: `https://api.us1.preludesecurity.com`

Do NOT ask which environment. Assume production. Only use a different URL if the user explicitly mentions us2, eu1, etc.

| Environment | API URL |
|-------------|---------|
| US1 (production) | `https://api.us1.preludesecurity.com` |
| US2 | `https://api.us2.preludesecurity.com` |
| EU1 | `https://api.eu1.preludesecurity.com` |

Write the keychain:
```ini
[default]
hq = https://api.us1.preludesecurity.com
account = <account_id>
handle = <email>
```
Add `oidc = <provider>` only if not `none`. Add `slug = <slug>` only if OIDC is `custom`.

### Step 4: Login

The user must run `prelude auth login` in their terminal (requires interactive input).

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

## CLI Domains for Detect

| Domain | Purpose |
|--------|---------|
| `detect` | Endpoint management, test scheduling, activity queries, threat hunts |
| `build` | Create/update security tests, threats, detections, threat hunt queries |
| `generate` | AI-powered test generation from threat intel |
| `partner` | Attach EDR integrations, deploy probes, get partner reports |

## Key Concepts

### Controls (Security Partners)

| Code | Name | Category |
|------|------|----------|
| 1 | CrowdStrike | XDR |
| 2 | Microsoft Defender | XDR |
| 3 | Splunk | SIEM |
| 4 | SentinelOne | XDR |
| 7 | Intune | Asset Manager |
| 9 | Okta | Identity |
| 11 | Entra | Identity |
| 12 | Jamf | Asset Manager |
| 17 | Tenable | Vuln Manager |
| 23 | Qualys | Vuln Manager |
| 25 | Rapid7 | Vuln Manager |
| 33 | Netskope | SASE |

### Run Codes (Scheduling)

| Code | Name |
|------|------|
| 1 | DAILY |
| 2 | WEEKLY |
| 3 | MONTHLY |
| 4 | SMART |
| 5 | DEBUG |
| 6 | RUN_ONCE |
| 10-16 | MONDAY-SUNDAY |

### Exit Codes (Test Results)

| Code | Meaning | State |
|------|---------|-------|
| 100 | PROTECTED | Protected |
| 137 | BLOCKED | Protected |
| 126 | EXECUTION_PREVENTED | Protected |
| 101 | UNPROTECTED | Unprotected |
| 102 | TIMED_OUT | Error |
| 104 | TEST_NOT_RELEVANT | Not Relevant |
| -1 | MISSING | None |

### Account Modes

| Code | Name | Behavior |
|------|------|----------|
| 0 | MANUAL | Tests only run when explicitly scheduled |
| 1 | FROZEN | No tests run |
| 2 | AUTOPILOT | Tests run automatically |

## Command Reference

### Endpoints

```bash
prelude detect endpoints                 # List endpoints (default: active in last 90 days)
prelude detect endpoints -d 30           # Active in last 30 days
prelude detect create-endpoint -h "hostname" -s "serial123" -r "<account_id>/<service_token>"
prelude detect create-endpoint -h "hostname" -s "serial123" -r "<account_id>/<service_token>" -t "tag1,tag2"
prelude detect update-endpoint <endpoint_id> -t "new_tag1,new_tag2"
prelude detect delete-endpoint <endpoint_id>
```

### Tests

```bash
prelude detect tests                     # List all security tests
prelude detect tests --techniques "T1059,T1053"  # Filter by MITRE techniques
prelude detect test <test_id>            # Get test details + attachments
prelude detect download <test_id>        # Download test files locally
prelude detect clone                     # Download ALL tests locally
```

### Threats

```bash
prelude detect threats                   # List all threats
prelude detect threat <threat_id>        # Get threat details
```

### Techniques

```bash
prelude detect techniques                # List all MITRE ATT&CK techniques
```

### Detections

```bash
prelude detect detections                # List all detection rules
prelude detect detection <detection_id>  # Get detection details
prelude detect detection <detection_id> -o rule.yaml  # Export Sigma rule
```

### Threat Hunts

```bash
prelude detect threat-hunts              # List all threat hunts
prelude detect threat-hunts --tests "test1,test2"
prelude detect threat-hunt <hunt_id>     # Get hunt details
prelude detect do-threat-hunt <hunt_id>  # Execute a threat hunt
```

### Scheduling

```bash
prelude detect queue                     # Show active test queue
prelude detect schedule <id> -t TEST                          # Schedule test (daily)
prelude detect schedule <id> -t TEST -r WEEKLY                # Schedule weekly
prelude detect schedule <id> -t TEST -r SMART --tags "prod"   # Smart schedule for tagged endpoints
prelude detect schedule <id> -t THREAT -r DAILY               # Schedule a threat
prelude detect unschedule <id> -t TEST                        # Remove from queue
prelude detect unschedule <id> -t THREAT --tags "prod"
```

### Activity & Results

```bash
prelude detect activity                  # Default: logs view, last 29 days

# Views: logs, tests, threats, endpoints, techniques, findings, metrics, protected
prelude detect activity --view tests
prelude detect activity --view endpoints
prelude detect activity --view protected
prelude detect activity --view findings
prelude detect activity --view metrics
prelude detect activity --view threats
prelude detect activity --view techniques

# Filters
prelude detect activity --view logs --start "2024-01-01" --finish "2024-01-31"
prelude detect activity --view tests --control CROWDSTRIKE
prelude detect activity --view logs --tests "test1,test2"
prelude detect activity --view logs --endpoints "ep1,ep2"
prelude detect activity --view logs --statuses "100,101"
prelude detect activity --view protected --social
```

### Build — Create & Manage Tests

```bash
# Tests
prelude build create-test -n "My Test" --unit "go" --technique "T1059.001"
prelude build clone-test <source_test_id>
prelude build update-test <test_id> -n "New Name"
prelude build update-test <test_id> --technique "T1059.001"
prelude build delete-test <test_id>
prelude build undelete-test <test_id>

# Upload test attachment
prelude build upload <test_id> -p /path/to/file.go
prelude build upload <test_id> -p /path/to/file.go --compile

# Threats
prelude build create-threat <directory> -n "Threat Name" --published "2024-01-01"
prelude build update-threat <threat_id> -n "Updated Name"
prelude build delete-threat <threat_id>

# Detection Rules (Sigma)
prelude build create-detection <test_id> -r /path/to/rule.yaml
prelude build update-detection <detection_id> -r /path/to/rule.yaml
prelude build delete-detection <detection_id>

# Threat Hunt Queries
prelude build create-threat-hunt <test_id> --name "Hunt Name" --query "query_string" --control CROWDSTRIKE
prelude build update-threat-hunt <hunt_id> --name "New Name" --query "new_query"
prelude build delete-threat-hunt <hunt_id>
```

### Generate — AI-Powered Test Generation

```bash
prelude generate threat-intel -f /path/to/report.pdf
prelude generate threat-intel -f /path/to/report.pdf --force_ai
prelude generate from-advisory CROWDSTRIKE --advisory_id "CS-2024-001"
```

### Partner — EDR Integration (for Detect)

```bash
# Deploy probes to partner hosts
prelude partner deploy CROWDSTRIKE --host_ids "id1,id2"

# Block a test (deploy detection rule to partner)
prelude partner block <test_id> -p CROWDSTRIKE

# Get partner detection reports
prelude partner reports CROWDSTRIKE -t <test_id>

# Get observed/detected statistics
prelude partner observed-detected
prelude partner observed-detected -t <test_id> --lookback 48

# List partner advisories
prelude partner advisories CROWDSTRIKE
prelude partner advisories CROWDSTRIKE --start "2024-01-01" --offset 0 --limit 50
```

### Jobs

```bash
prelude jobs background-jobs             # List all background jobs
prelude jobs background-job <job_id>     # Get specific job status
```

## Common Workflows

### Quick Health Check

Use `list_accounts`, `list_endpoints`, and `get_activity` (MCP) or:
```bash
prelude iam account                      # Verify connection
prelude detect endpoints                 # See active endpoints
prelude detect queue                     # See scheduled tests
prelude detect activity --view protected # See protection status
```

### Investigate Test Results

Use `get_activity` with appropriate view and filters (MCP) or:
```bash
prelude detect activity --view logs --start "2024-01-01" --finish "2024-01-31"
prelude detect activity --view tests --control CROWDSTRIKE
prelude detect activity --view findings
```

### Set Up Continuous Testing

Use `list_tests`, `schedule`, and `get_activity` (MCP) or:
```bash
prelude detect tests                                     # Browse available tests
prelude detect schedule <test_id> -t TEST -r DAILY       # Schedule daily
prelude detect queue                                     # Verify queued
prelude detect activity --view logs                      # Check results later
```

### Create Custom Test

Use `create_test`, `compile_code`, and `schedule` (MCP) or:
```bash
prelude build create-test -n "My Custom Test" --unit "go" --technique "T1059.001"
prelude build upload <test_id> -p ./test.go --compile
prelude detect schedule <test_id> -t TEST -r DAILY
```

### Deploy Probes via Partner

Use `list_endpoints` to verify after deploying (MCP) or:
```bash
prelude partner endpoints CROWDSTRIKE --platform windows
prelude partner deploy CROWDSTRIKE --host_ids "host1,host2"
prelude detect endpoints  # Verify new endpoints appear
```

## Naming Differences: CLI vs Platform UI

| CLI Term | Platform UI Term |
|----------|-----------------|
| `endpoint` / `probe` | Endpoint |
| `control` | Partner / Integration |
| `dos` | Platform (e.g., `windows-x86_64`) |
| `queue` | Schedule |
| `activity` | Results / Reports |
| `technique` | MITRE Technique |
| `threat` | Threat (collection of tests) |
| `detection` | Detection Rule (Sigma YAML) |
| `threat-hunt` | Threat Hunt |

## Error Handling

- **401 Unauthorized**: Run `prelude auth login` or `prelude auth refresh`
- **"Please make sure you are using an up-to-date profile"**: Run `prelude configure` and ensure `handle` is set
- **Python version error**: CLI requires Python 3.10+
- **Connection errors**: Check `hq` URL in `~/.prelude/keychain.ini`

## Tips

- Use `--resolve_enums` for human-readable output
- Pipe JSON to `jq` for filtering: `prelude detect tests 2>/dev/null | jq '.[] | .name'`
- CLI outputs JSON by default — great for scripting
- Service users (`iam create-service-user`) are ideal for CI/CD
