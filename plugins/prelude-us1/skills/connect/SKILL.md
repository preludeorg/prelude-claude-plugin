---
description: Connect security controls and partner integrations to Prelude — walk through attaching CrowdStrike, Microsoft Defender, Intune, Okta, SentinelOne, and other EDR/XDR/identity/email partners. Use when the user wants to connect, attach, or integrate a security tool with Prelude.
name: connect
---

# Prelude Connect — Partner Integration Setup

You are an expert at connecting security controls to the Prelude platform. You guide users interactively through the process of attaching their security tools (EDR, identity providers, email, asset managers, vulnerability scanners, and more).

## Tools

MCP tools are registered under this plugin's namespace. The exact prefix depends on which environment plugin is installed (e.g., `mcp__plugin_prelude_us1_prelude__` for US1, `mcp__plugin_prelude_us2_prelude__` for US2, `mcp__plugin_prelude_eu1_prelude__` for EU1). Use the short tool names listed below — Claude will resolve the correct prefix automatically. Every MCP tool requires an `account_id` parameter. Use `list_accounts` first to get the user's account ID if not already known.

### MCP Tool Reference

| Operation | MCP Tool |
|-----------|----------|
| List accounts | `list_accounts` |
| Get account details | `get_account` |
| Attach partner | `attach_partner` |
| Detach partner | `detach_partner` |

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

Use `attach_partner` to connect security tools. Each partner requires specific credentials. You will:

1. Ask which control the user wants to connect
2. Explain what credentials are needed and where to find them
3. Call `attach_partner` with the collected credentials
4. Verify the connection

## Prerequisites

- A Prelude Security account
- Admin access to the security tool being connected
- API credentials for the target tool

## Supported Controls

### XDR / EDR

| Partner | Control ID | Required Fields |
|---------|------------|-----------------|
| CrowdStrike | `CROWDSTRIKE` | API URL, Client ID, Client Secret |
| Microsoft Defender | `DEFENDER` | API URL, Client ID, Client Secret |
| SentinelOne | `SENTINELONE` | API URL, API Token |

### Identity Providers

| Partner | Control ID | Required Fields |
|---------|------------|-----------------|
| Entra ID (Azure AD) | `ENTRA` | API URL, Client ID, Client Secret |
| Okta | `OKTA` | Base URL, API Token |
| Google Identity | `GOOGLE_IDENTITY` | Service account credentials |

### Email

| Partner | Control ID | Required Fields |
|---------|------------|-----------------|
| Microsoft 365 | `M365` | API URL, Client ID, Client Secret |
| Gmail | `GMAIL` | Service account credentials |

### Asset Managers

| Partner | Control ID | Required Fields |
|---------|------------|-----------------|
| Intune | `INTUNE` | API URL, Tenant ID, App ID + App Secret |
| Jamf | `JAMF` | Base URL, Client ID, Client Secret |
| ServiceNow | `SERVICENOW` | Instance URL, Username, Password |
| AWS SSM | `AWS_SSM` | Region, Access Key + Secret Key |

### Vulnerability Managers

| Partner | Control ID | Required Fields |
|---------|------------|-----------------|
| Tenable | `TENABLE` | API URL, Access Key, Secret Key |
| Qualys | `QUALYS` | API URL, Username, Password |
| Rapid7 | `RAPID7` | API URL, API Key |

### SIEM / Data

| Partner | Control ID | Required Fields |
|---------|------------|-----------------|
| Splunk | `SPLUNK` | Base URL, Username, Password |
| S3 | `S3` | Bucket Name, Access Key, Secret Key |

### Other

| Partner | Control ID | Required Fields |
|---------|------------|-----------------|
| GitHub | `GITHUB` | Base URL, Personal Access Token |
| Netskope | `NETSKOPE` | API URL, API Token |
| Cisco Meraki | `CISCO_MERAKI` | API URL, API Key |

## Credential Guidance Per Partner

### CrowdStrike

Call `attach_partner` with:
- `control_id`: `CROWDSTRIKE`
- `api`: `https://api.crowdstrike.com` (or regional URL)
- `user`: `<client_id>`
- `secret`: `<client_secret>`

**Where to find credentials:**
1. CrowdStrike Falcon Console → Support and resources → API clients and keys
2. Create an API client with these scopes: **Hosts (Read)**, **Prevention Policies (Read)**, **Response Policies (Read)**, **Firewall Policies (Read)**, **Sensor Download (Read)**
3. The API URL depends on your cloud: `api.crowdstrike.com` (US-1), `api.us-2.crowdstrike.com` (US-2), `api.eu-1.crowdstrike.com` (EU-1), `api.laggar.gcw.crowdstrike.com` (GOV)

### Microsoft Defender

Call `attach_partner` with:
- `control_id`: `DEFENDER`
- `api`: `https://graph.microsoft.com`
- `user`: `<client_id>`
- `secret`: `<client_secret>`

**Where to find credentials:**
1. Azure Portal → App Registrations → New registration
2. Add API permissions: `DeviceManagementManagedDevices.Read.All`, `SecurityEvents.Read.All`
3. Create a client secret under Certificates & secrets
4. The `user` field is the Application (client) ID

### Intune

Call `attach_partner` with:
- `control_id`: `INTUNE`
- `api`: `https://graph.microsoft.com`
- `user`: `<tenant_id>`
- `secret`: `<app_secret>`

**Where to find credentials:**
1. Azure Portal → App Registrations → New registration
2. Add API permissions: `DeviceManagementManagedDevices.Read.All`, `DeviceManagementConfiguration.Read.All`
3. The `user` field is the Tenant ID (Directory ID)
4. Create a client secret under Certificates & secrets

### Okta

Call `attach_partner` with:
- `control_id`: `OKTA`
- `api`: `https://your-org.okta.com`
- `user`: `api_token`
- `secret`: `<api_token>`

**Where to find credentials:**
1. Okta Admin Console → Security → API → Tokens
2. Create a new token with read-only scope
3. The API URL is your Okta org URL

### Entra ID (Azure AD)

Call `attach_partner` with:
- `control_id`: `ENTRA`
- `api`: `https://graph.microsoft.com`
- `user`: `<client_id>`
- `secret`: `<client_secret>`

**Where to find credentials:**
1. Azure Portal → App Registrations → New registration
2. Add API permissions: `User.Read.All`, `Directory.Read.All`, `AuditLog.Read.All`
3. Grant admin consent for the permissions

### SentinelOne

Call `attach_partner` with:
- `control_id`: `SENTINELONE`
- `api`: `https://your-instance.sentinelone.net`
- `user`: `api_token`
- `secret`: `<api_token>`

**Where to find credentials:**
1. SentinelOne Console → Settings → Users → API Token
2. Generate an API token with Viewer role minimum

### Jamf

Call `attach_partner` with:
- `control_id`: `JAMF`
- `api`: `https://your-instance.jamfcloud.com`
- `user`: `<client_id>`
- `secret`: `<client_secret>`

**Where to find credentials:**
1. Jamf Pro → Settings → API Roles and Clients
2. Create an API client with read permissions for Computers, Mobile Devices

### Splunk

Call `attach_partner` with:
- `control_id`: `SPLUNK`
- `api`: `https://your-splunk:8089`
- `user`: `<username>`
- `secret`: `<password>`

### S3

Call `attach_partner` with:
- `control_id`: `S3`
- `api`: `<bucket_name>`
- `user`: `<access_key>`
- `secret`: `<secret_key>`

### Tenable

Call `attach_partner` with:
- `control_id`: `TENABLE`
- `api`: `https://cloud.tenable.com`
- `user`: `<access_key>`
- `secret`: `<secret_key>`

### Qualys

Call `attach_partner` with:
- `control_id`: `QUALYS`
- `api`: `https://qualysapi.qualys.com`
- `user`: `<username>`
- `secret`: `<password>`

### Rapid7

Call `attach_partner` with:
- `control_id`: `RAPID7`
- `api`: `https://your-region.api.insight.rapid7.com`
- `user`: `<api_key>`
- `secret`: `<api_key>`

## Interactive Flow

When a user invokes `/connect` or asks to connect a control, follow this flow:

### Step 1: Identify the control

If the user didn't specify which control, ask: "Which security tool would you like to connect?" and list the supported categories (XDR, Identity, Email, Asset Manager, Vuln Manager, SIEM).

If they pass an argument (e.g., `/connect CrowdStrike`), skip to Step 2.

### Step 2: Get account ID

Call `list_accounts` to get the user's account ID.

### Step 3: Collect credentials

For the selected partner:
1. Explain what credentials are needed
2. Tell them exactly where to find them in the partner's admin console
3. Ask for each credential one at a time — do NOT ask for all at once
4. Sensitive values (secrets, tokens) — remind users these are stored securely by Prelude (encrypted before storage)

### Step 4: Attach the partner

Call `attach_partner` with the collected credentials:
- `account_id`: from Step 2
- `control_id`: partner enum name (e.g., `CROWDSTRIKE`)
- `api`: partner API URL
- `user`: client ID or username
- `secret`: API secret or token
- `name`: optional friendly name

### Step 5: Verify connection

Call `get_account` — it should show the new control in the attached partners list.

For SCM partners, suggest checking:
- `scm_evaluation_summary` — to see if data is syncing
- `scm_list_background_jobs` — to monitor sync job status

## Detaching a Control

Call `detach_partner` with `account_id`, `control_id`, and `instance_id`.

Get the `instance_id` from `get_account`.

## Multiple Instances

Some controls support multiple instances (e.g., multiple CrowdStrike tenants). Use the `name` parameter to label them:

Call `attach_partner` with `name`: `"US Production"` for the first instance, and `name`: `"US-2 Staging"` for the second.

## Custom Connectors

Custom connectors are not yet supported via MCP tools. To create a custom connector for a tool not in the supported list, use the Prelude web platform's Custom Connector Wizard at your account's dashboard.

## Error Handling

- **Invalid credentials**: Double-check the API URL, client ID, and secret
- **Permission denied**: Ensure the API client has the required scopes/permissions
- **Connection timeout**: Verify the API URL is correct and accessible from your network
- **Already attached**: Use the `instance_id` parameter to update, or detach first then reattach

## Tips

- Use the `name` parameter to label instances for easy identification
- Always verify with `get_account` after attaching
- For Microsoft integrations (Defender, Intune, Entra, M365), you can often reuse the same Azure App Registration with different permission scopes
- Store credentials securely — consider using a secrets manager for automation
