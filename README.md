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
- [Prelude CLI](https://docs.preludesecurity.com) v2.6+ (`pip install prelude-cli`)
- A Prelude Security account

## License

MIT
