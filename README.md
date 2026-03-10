# Prelude Claude Plugin

Official Claude Code plugin for [Prelude Security](https://preludesecurity.com) — continuous security testing, posture monitoring, compliance reporting, and partner integrations.

## Installation

### From the terminal

```bash
# Step 1: Add the marketplace
claude plugin marketplace add preludeorg/prelude-claude-plugin

# Step 2: Install the plugin
claude plugin install prelude@prelude-claude-plugin
```

### From inside Claude Code

```bash
# Step 1: Add the marketplace
/plugin marketplace add preludeorg/prelude-claude-plugin

# Step 2: Install the plugin
/plugin install prelude@prelude-claude-plugin
```

### Scope options

By default, the plugin installs to **user scope** (available in all projects). You can change this:

```bash
# Project scope (shared with team via version control)
claude plugin install prelude@prelude-claude-plugin --scope project

# Local scope (this machine only, gitignored)
claude plugin install prelude@prelude-claude-plugin --scope local
```

### Management

```bash
claude plugin list                                          # List installed plugins
claude plugin update prelude@prelude-claude-plugin          # Update to latest
claude plugin disable prelude@prelude-claude-plugin         # Disable without uninstalling
claude plugin enable prelude@prelude-claude-plugin          # Re-enable
claude plugin uninstall prelude@prelude-claude-plugin       # Uninstall
```

## Skills

| Skill | Description |
|-------|-------------|
| `/prelude:detect` | Continuous security testing — manage endpoints, schedule tests, view results, create threats and detections |
| `/prelude:monitor` | Security posture monitoring — query endpoints/users/inboxes, evaluate policies, manage exceptions, generate reports |
| `/prelude:connect` | Connect security controls — walk through attaching CrowdStrike, Defender, Intune, Okta, and 20+ other integrations |
| `/prelude:nist` | NIST CSF 2.0 compliance report — interactive assessment across all 6 functions with PDF output |
| `/prelude:cyberinsurance` | Cyber insurance readiness report — interactive questionnaire across 12 domains with PDF output |

## Prerequisites

- [Claude Code](https://claude.ai/code) installed
- [Prelude CLI](https://docs.preludesecurity.com) v2.6+ (`pip install prelude-cli`)
- A Prelude Security account

## License

MIT
