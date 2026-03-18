# Prelude Claude Plugin

Official Claude Code plugin for [Prelude Security](https://preludesecurity.com) — continuous security testing, posture monitoring, compliance reporting, and partner integrations.

## Installation

Choose the plugin for your Prelude environment:

| Plugin | Environment | MCP Server |
|--------|-------------|------------|
| `prelude-us1` | US1 | `mcp.us1.preludesecurity.com` |
| `prelude-us2` | US2 | `mcp.us2.preludesecurity.com` |
| `prelude-eu1` | EU1 | `mcp.eu1.preludesecurity.com` |

### From the terminal

```bash
# Step 1: Add the marketplace
claude plugin marketplace add preludeorg/prelude-claude-plugin

# Step 2: Install the plugin for your environment
claude plugin install prelude-us1@prelude-claude-plugin    # US1
claude plugin install prelude-us2@prelude-claude-plugin    # US2
claude plugin install prelude-eu1@prelude-claude-plugin    # EU1
```

### From inside Claude Code

```bash
# Step 1: Add the marketplace
/plugin marketplace add preludeorg/prelude-claude-plugin

# Step 2: Install the plugin for your environment
/plugin install prelude-us1@prelude-claude-plugin    # US1
/plugin install prelude-us2@prelude-claude-plugin    # US2
/plugin install prelude-eu1@prelude-claude-plugin    # EU1
```

### Enable auto-updates

By default, third-party marketplace plugins do not auto-update. To enable automatic updates, either:

1. **Via the UI:** Run `/plugin` → select the **Marketplaces** tab → choose the marketplace → **Enable auto-update**

2. **Via settings:** Add to your `.claude/settings.json`:
   ```json
   {
     "extraKnownMarketplaces": {
       "prelude-claude-plugin": {
         "source": {
           "source": "github",
           "repo": "preludeorg/prelude-claude-plugin"
         },
         "autoUpdate": true
       }
     }
   }
   ```

## Skills

| Skill | Description |
|-------|-------------|
| `/detect` | Continuous security testing — manage endpoints, schedule tests, view results, create threats and detections |
| `/monitor` | Security posture monitoring — query endpoints/users/inboxes, evaluate policies, manage exceptions, generate reports |
| `/connect` | Connect security controls — walk through attaching CrowdStrike, Defender, Intune, Okta, and 20+ other integrations |
| `/nist` | NIST CSF 2.0 compliance report — interactive assessment across all 6 functions with PDF output |
| `/cyberinsurance` | Cyber insurance readiness report — interactive questionnaire across 12 domains with PDF output |

## Prerequisites

- [Claude Code](https://claude.ai/code) installed
- A Prelude Security account
- Optional: [Prelude CLI](https://docs.preludesecurity.com) v2.6+ (`pip install prelude-cli`) — the MCP server handles most operations, but some features (file downloads, CSV exports, AI-powered test generation) require the CLI

## License

MIT
