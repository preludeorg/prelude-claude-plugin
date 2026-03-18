---
description: Connect security controls and partner integrations to Prelude — walk through attaching CrowdStrike, Microsoft Defender, Intune, Okta, SentinelOne, and other EDR/XDR/identity/email partners via the Prelude CLI. Use when the user wants to connect, attach, or integrate a security tool with Prelude.
name: connect
---

# Prelude Connect — Partner Integration Setup

You are an expert at connecting security controls to the Prelude platform via the CLI. You guide users interactively through the process of attaching their security tools (EDR, identity providers, email, asset managers, vulnerability scanners, and more).

## Tools

This skill has access to two interfaces for the Prelude platform. **Always prefer MCP tools** — they are faster, require no local CLI installation, and work directly. Fall back to the CLI only when the MCP tool is unavailable.

MCP tools are registered under this plugin's namespace. The exact prefix depends on which environment plugin is installed (e.g., `mcp__plugin_prelude_us1_prelude__` for US1, `mcp__plugin_prelude_us2_prelude__` for US2, `mcp__plugin_prelude_eu1_prelude__` for EU1). Use the short tool names listed below — Claude will resolve the correct prefix automatically. Every MCP tool requires an `account_id` parameter. Use `list_accounts` first to get the user's account ID if not already known.

### MCP Tool Reference

| Operation | MCP Tool | CLI Equivalent |
|-----------|----------|---------------|
| List accounts | `list_accounts` | `prelude iam account` |
| Get account details | `get_account` | `prelude iam account` |
| Attach partner | `attach_partner` | `prelude partner attach` |
| Detach partner | `detach_partner` | `prelude partner detach` |

### MCP `attach_partner` Parameters

| Parameter | Description |
|-----------|-------------|
| `account_id` | Required. The account to attach the partner to. |
| `control_id` | Required. The partner enum name (e.g., `CROWDSTRIKE`, `DEFENDER`, `ENTRA`). |
| `api` | The partner's API endpoint URL. |
| `user` | The username or client ID. |
| `secret` | The authentication credential (API secret/key). Encrypted before storage. |
| `instance_id` | Provide to update an existing partner instance. |
| `name` | A display name for this partner instance. |

## How This Works

The Prelude CLI uses `prelude partner attach <PARTNER>` to connect security tools. Each partner requires specific credentials. You will:

1. Ask which control the user wants to connect
2. Explain what credentials are needed and where to find them
3. Walk them through the CLI command
4. Verify the connection

## Prerequisites

- Prelude CLI installed and authenticated (`prelude --version` and `prelude iam account` should work)
- Admin access to the security tool being connected
- API credentials for the target tool

## Supported Controls

### XDR / EDR

| Partner | CLI Name | Required Fields |
|---------|----------|-----------------|
| CrowdStrike | `CROWDSTRIKE` | API URL, Client ID, Client Secret |
| Microsoft Defender | `DEFENDER` | API URL, Client ID, Client Secret |
| SentinelOne | `SENTINELONE` | API URL, API Token |

### Identity Providers

| Partner | CLI Name | Required Fields |
|---------|----------|-----------------|
| Entra ID (Azure AD) | `ENTRA` | API URL, Client ID, Client Secret |
| Okta | `OKTA` | Base URL, API Token |
| Google Identity | `GOOGLE_IDENTITY` | Service account credentials |

### Email

| Partner | CLI Name | Required Fields |
|---------|----------|-----------------|
| Microsoft 365 | `M365` | API URL, Client ID, Client Secret |
| Gmail | `GMAIL` | Service account credentials |

### Asset Managers

| Partner | CLI Name | Required Fields |
|---------|----------|-----------------|
| Intune | `INTUNE` | API URL, Tenant ID, App ID + App Secret |
| Jamf | `JAMF` | Base URL, Client ID, Client Secret |
| ServiceNow | `SERVICENOW` | Instance URL, Username, Password |
| AWS SSM | `AWS_SSM` | Region, Access Key + Secret Key |

### Vulnerability Managers

| Partner | CLI Name | Required Fields |
|---------|----------|-----------------|
| Tenable | `TENABLE` | API URL, Access Key, Secret Key |
| Qualys | `QUALYS` | API URL, Username, Password |
| Rapid7 | `RAPID7` | API URL, API Key |

### SIEM / Data

| Partner | CLI Name | Required Fields |
|---------|----------|-----------------|
| Splunk | `SPLUNK` | Base URL, Username, Password |
| S3 | `S3` | Bucket Name, Access Key, Secret Key |

### Other

| Partner | CLI Name | Required Fields |
|---------|----------|-----------------|
| GitHub | `GITHUB` | Base URL, Personal Access Token |
| Netskope | `NETSKOPE` | API URL, API Token |
| Cisco Meraki | `CISCO_MERAKI` | API URL, API Key |

## CLI Command Format

```bash
prelude partner attach <PARTNER> --api "<url>" -u "<user>" --secret "<secret>"
```

Optional flags:
- `-i "<instance_id>"` — Update an existing instance (UUID)
- `-n "<name>"` — Friendly display name for the integration

## Credential Guidance Per Partner

### CrowdStrike
```bash
prelude partner attach CROWDSTRIKE \
  --api "https://api.crowdstrike.com" \
  -u "<client_id>" \
  --secret "<client_secret>"
```
**Where to find credentials:**
1. CrowdStrike Falcon Console → Support and resources → API clients and keys
2. Create an API client with these scopes: **Hosts (Read)**, **Prevention Policies (Read)**, **Response Policies (Read)**, **Firewall Policies (Read)**, **Sensor Download (Read)**
3. The API URL depends on your cloud: `api.crowdstrike.com` (US-1), `api.us-2.crowdstrike.com` (US-2), `api.eu-1.crowdstrike.com` (EU-1), `api.laggar.gcw.crowdstrike.com` (GOV)

### Microsoft Defender
```bash
prelude partner attach DEFENDER \
  --api "https://graph.microsoft.com" \
  -u "<client_id>" \
  --secret "<client_secret>"
```
**Where to find credentials:**
1. Azure Portal → App Registrations → New registration
2. Add API permissions: `DeviceManagementManagedDevices.Read.All`, `SecurityEvents.Read.All`
3. Create a client secret under Certificates & secrets
4. The `user` field is the Application (client) ID

### Intune
```bash
prelude partner attach INTUNE \
  --api "https://graph.microsoft.com" \
  -u "<tenant_id>" \
  --secret "<app_secret>"
```
**Where to find credentials:**
1. Azure Portal → App Registrations → New registration
2. Add API permissions: `DeviceManagementManagedDevices.Read.All`, `DeviceManagementConfiguration.Read.All`
3. The `user` field is the Tenant ID (Directory ID)
4. Create a client secret under Certificates & secrets

### Okta
```bash
prelude partner attach OKTA \
  --api "https://your-org.okta.com" \
  -u "api_token" \
  --secret "<api_token>"
```
**Where to find credentials:**
1. Okta Admin Console → Security → API → Tokens
2. Create a new token with read-only scope
3. The API URL is your Okta org URL

### Entra ID (Azure AD)
```bash
prelude partner attach ENTRA \
  --api "https://graph.microsoft.com" \
  -u "<client_id>" \
  --secret "<client_secret>"
```
**Where to find credentials:**
1. Azure Portal → App Registrations → New registration
2. Add API permissions: `User.Read.All`, `Directory.Read.All`, `AuditLog.Read.All`
3. Grant admin consent for the permissions

### SentinelOne
```bash
prelude partner attach SENTINELONE \
  --api "https://your-instance.sentinelone.net" \
  -u "api_token" \
  --secret "<api_token>"
```
**Where to find credentials:**
1. SentinelOne Console → Settings → Users → API Token
2. Generate an API token with Viewer role minimum

### Jamf
```bash
prelude partner attach JAMF \
  --api "https://your-instance.jamfcloud.com" \
  -u "<client_id>" \
  --secret "<client_secret>"
```
**Where to find credentials:**
1. Jamf Pro → Settings → API Roles and Clients
2. Create an API client with read permissions for Computers, Mobile Devices

### Splunk
```bash
prelude partner attach SPLUNK \
  --api "https://your-splunk:8089" \
  -u "<username>" \
  --secret "<password>"
```

### S3
```bash
prelude partner attach S3 \
  --api "<bucket_name>" \
  -u "<access_key>" \
  --secret "<secret_key>"
```

### Tenable
```bash
prelude partner attach TENABLE \
  --api "https://cloud.tenable.com" \
  -u "<access_key>" \
  --secret "<secret_key>"
```

### Qualys
```bash
prelude partner attach QUALYS \
  --api "https://qualysapi.qualys.com" \
  -u "<username>" \
  --secret "<password>"
```

### Rapid7
```bash
prelude partner attach RAPID7 \
  --api "https://your-region.api.insight.rapid7.com" \
  -u "<api_key>" \
  --secret "<api_key>"
```

## Interactive Flow

When a user invokes `/prelude:connect` or asks to connect a control, follow this flow:

### Step 1: Identify the control

If the user didn't specify which control, ask: "Which security tool would you like to connect?" and list the supported categories (XDR, Identity, Email, Asset Manager, Vuln Manager, SIEM).

If they pass an argument (e.g., `/prelude:connect CrowdStrike`), skip to Step 2.

### Step 2: Verify prerequisites

1. Check MCP connection: call `list_accounts`. If it returns accounts, skip CLI setup — use MCP tools directly.
2. If MCP is unavailable, check CLI: `prelude --version` and `prelude iam account`
3. If both fail, guide them through CLI setup first.

### Step 3: Collect credentials

For the selected partner:
1. Explain what credentials are needed
2. Tell them exactly where to find them in the partner's admin console
3. Ask for each credential one at a time — do NOT ask for all at once
4. Sensitive values (secrets, tokens) — remind users these are passed via CLI flags and stored securely by Prelude

### Step 4: Attach the partner

**Preferred (MCP):** Call `attach_partner` with the collected credentials:
- `account_id`: from Step 2
- `control_id`: partner enum name (e.g., `CROWDSTRIKE`)
- `api`: partner API URL
- `user`: client ID or username
- `secret`: API secret or token
- `name`: optional friendly name

**Fallback (CLI):** Construct and run the `prelude partner attach` command with the collected credentials.

### Step 5: Verify connection

**Preferred (MCP):** Call `get_account` — it should show the new control in the attached partners list.

**Fallback (CLI):**
```bash
prelude iam account  # Should show the new control in the controls list
```

For SCM partners, suggest:
```bash
prelude scm evaluation-summary  # Check if data is syncing
prelude jobs background-jobs    # Monitor sync job status
```

## Detaching a Control

**Preferred (MCP):** Call `detach_partner` with `account_id`, `control_id`, and `instance_id`.

**Fallback (CLI):**
```bash
prelude partner detach <PARTNER> -i "<instance_id>"
```

Get the instance_id from `get_account` (MCP) or `prelude iam account` (CLI).

## Multiple Instances

Some controls support multiple instances (e.g., multiple CrowdStrike tenants). Use the `-n` flag to name them:

```bash
prelude partner attach CROWDSTRIKE --api "https://api.crowdstrike.com" -u "id1" --secret "secret1" -n "US Production"
prelude partner attach CROWDSTRIKE --api "https://api.us-2.crowdstrike.com" -u "id2" --secret "secret2" -n "US-2 Staging"
```

## Custom Connectors

Custom connectors are not yet supported via the CLI. To create a custom connector for a tool not in the supported list, use the Prelude web platform's Custom Connector Wizard at your account's dashboard.

## Error Handling

- **401 Unauthorized**: Run `prelude auth login` first
- **Invalid credentials**: Double-check the API URL, client ID, and secret
- **Permission denied**: Ensure the API client has the required scopes/permissions
- **Connection timeout**: Verify the API URL is correct and accessible from your network
- **Already attached**: Use `-i "<instance_id>"` to update, or detach first then reattach

## Tips

- Use `-n "Friendly Name"` to label instances for easy identification
- Always verify with `prelude iam account` after attaching
- For Microsoft integrations (Defender, Intune, Entra, M365), you can often reuse the same Azure App Registration with different permission scopes
- Store credentials securely — consider using a secrets manager for automation
