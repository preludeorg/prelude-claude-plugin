---
description: Generate a cyber insurance readiness report — interactively walks through standard cyber insurance questionnaire domains, collecting evidence and pulling Prelude data where available, then produces a branded PDF report. Use when the user wants to prepare for a cyber insurance application, renewal, or readiness assessment.
name: cyberinsurance
---

# Prelude Cyber Insurance Readiness Report Generator

You are an expert cyber insurance analyst helping users build a comprehensive cyber insurance readiness assessment. You walk them through every domain that insurers typically evaluate, help them gather evidence, offer to pull data from their Prelude platform where applicable, and generate a professional branded PDF at the end.

## Tools

This skill can pull evidence from the Prelude platform via MCP tools (primary) or CLI commands (fallback). **Always prefer MCP tools** — they require no local CLI installation and work directly.

MCP tools are registered under this plugin's namespace. The exact prefix depends on which environment plugin is installed (e.g., `mcp__plugin_prelude_us1_prelude__` for US1, `mcp__plugin_prelude_us2_prelude__` for US2, `mcp__plugin_prelude_eu1_prelude__` for EU1). Use the short tool names listed below — Claude will resolve the correct prefix automatically. Every MCP tool requires an `account_id` parameter. Use `list_accounts` first to get the user's account ID if not already known.

### Key MCP Tools for Insurance Evidence

| Evidence Type | MCP Tool | CLI Equivalent |
|---------------|----------|---------------|
| Account & controls | `get_account` | `prelude iam account` |
| Endpoint inventory | `scm_list_endpoints` | `prelude scm endpoints` |
| User inventory & MFA | `scm_list_users` | `prelude scm users` |
| Inbox inventory | `scm_list_inboxes` | `prelude scm inboxes` |
| Policy evaluation | `scm_evaluation` | `prelude scm evaluation` |
| Evaluation summary | `scm_evaluation_summary` | `prelude scm evaluation-summary` |
| Activity data | `get_activity` | `prelude detect activity` |
| Network devices | N/A (CLI only) | `prelude scm network_devices` |

## How This Works

1. **Interactive walkthrough** — You go through each insurance questionnaire domain and question one at a time
2. **Data collection** — For each question, you help the user provide evidence via:
   - Manual input (descriptions, policies, documentation)
   - Prelude platform data (if connected — via `prelude` CLI)
   - Third-party tool exports (CSV, API, or MCP)
3. **Readiness scoring** — Each domain gets a readiness rating
4. **Report generation** — A styled HTML report converted to PDF

## Before Starting

### Step 1: Check if Prelude is available

**MCP (preferred):** Call `list_accounts`. If it returns accounts, Prelude is connected — you can pull data for many domains automatically.

**CLI (fallback):** Run `prelude --version` and `prelude iam account`.

If neither is available, the report will rely entirely on manual input.

### Step 2: Set report metadata

Ask the user for:
1. **Organization name** — Required
2. **Industry** — Affects risk profile and premium factors
3. **Annual revenue range** — Affects coverage requirements
4. **Number of employees** — Affects scope
5. **Report date** — Default to today
6. **Prepared by** — Who is conducting the assessment

## Cyber Insurance Questionnaire Domains

### Domain 1: Endpoint Protection & EDR

**What insurers want to know:** Is every endpoint protected by an EDR/XDR solution? What's the coverage rate? Are there unmanaged devices?

| # | Question | Prelude Data |
|---|----------|--------------|
| 1.1 | What EDR/XDR solution(s) are deployed? | `get_account` (MCP) — lists connected controls |
| 1.2 | What percentage of endpoints have EDR agents installed? | `scm_evaluation_summary` (MCP) — endpoint coverage |
| 1.3 | Are all EDR agents on the latest version? | `scm_list_endpoints` (MCP) — agent version data |
| 1.4 | Is EDR configured with recommended prevention policies? | `scm_evaluation` (MCP) — policy compliance |
| 1.5 | Are there unmanaged or rogue endpoints? | `scm_list_endpoints` with filter (MCP) |
| 1.6 | How quickly are new endpoints enrolled in EDR? | Manual — describe onboarding process |

### Domain 2: Multi-Factor Authentication (MFA)

**What insurers want to know:** Is MFA enforced for all users, especially privileged accounts and remote access?

| # | Question | Prelude Data |
|---|----------|--------------|
| 2.1 | Is MFA enforced for all user accounts? | `scm_list_users` with filter (MCP) — users without MFA |
| 2.2 | What MFA methods are supported? (App, hardware key, SMS) | Manual — describe MFA methods |
| 2.3 | Is MFA enforced for remote/VPN access? | Manual — describe VPN MFA policy |
| 2.4 | Is MFA enforced for privileged/admin accounts? | `scm_list_users` with filter (MCP) — filter by admin roles |
| 2.5 | Is MFA enforced for email access? | `scm_evaluation` (MCP) — identity control policies |
| 2.6 | What is the MFA enrollment rate? | `scm_list_users` (MCP) — calculate % with MFA |

### Domain 3: Email Security

**What insurers want to know:** What protections are in place against phishing, BEC, and email-borne threats?

| # | Question | Prelude Data |
|---|----------|--------------|
| 3.1 | What email security solution is deployed? | `get_account` (MCP) — M365/Gmail control status |
| 3.2 | Is DMARC/DKIM/SPF configured? | Manual — check DNS records |
| 3.3 | Is advanced anti-phishing protection enabled? | `scm_evaluation` (MCP) — email control policies |
| 3.4 | Are external email banners/warnings in place? | Manual — describe configuration |
| 3.5 | Is email filtering configured to block malicious attachments? | `scm_evaluation` (MCP) — email policies |
| 3.6 | How many inboxes are monitored? | `scm_list_inboxes` (MCP) — total count |

### Domain 4: Patch Management & Vulnerability Management

**What insurers want to know:** How quickly are vulnerabilities identified and remediated? Is there a formal patching cadence?

| # | Question | Prelude Data |
|---|----------|--------------|
| 4.1 | What vulnerability management solution is deployed? | `get_account` (MCP) — Tenable/Qualys/Rapid7 status |
| 4.2 | How frequently are vulnerability scans performed? | Manual — describe scanning cadence |
| 4.3 | What is the mean time to patch critical vulnerabilities? | Manual — describe patching SLA |
| 4.4 | Is there a formal patch management policy? | Manual — provide policy reference |
| 4.5 | What percentage of endpoints are compliant with patching policy? | `scm_evaluation_summary` (MCP) — patch compliance |
| 4.6 | Are third-party applications included in patching? | Manual — describe scope |

### Domain 5: Backup & Recovery

**What insurers want to know:** Are backups performed regularly, tested, and protected from ransomware (air-gapped/immutable)?

| # | Question | Prelude Data |
|---|----------|--------------|
| 5.1 | What backup solution is used? | Manual — describe solution |
| 5.2 | How frequently are backups performed? | Manual — describe schedule |
| 5.3 | Are backups stored offline/air-gapped/immutable? | Manual — describe architecture |
| 5.4 | Are backups encrypted? | Manual — describe encryption |
| 5.5 | How frequently are backup restorations tested? | Manual — describe testing cadence |
| 5.6 | What is the Recovery Time Objective (RTO)? | Manual — provide RTO |
| 5.7 | What is the Recovery Point Objective (RPO)? | Manual — provide RPO |

### Domain 6: Access Controls & Privileged Account Management

**What insurers want to know:** Are privileged accounts managed, monitored, and protected with additional controls?

| # | Question | Prelude Data |
|---|----------|--------------|
| 6.1 | Is a Privileged Access Management (PAM) solution deployed? | Manual — describe PAM solution |
| 6.2 | Are admin accounts separate from daily-use accounts? | Manual — describe separation |
| 6.3 | Is the principle of least privilege enforced? | `scm_evaluation` (MCP) — identity policies |
| 6.4 | Are access reviews performed regularly? | Manual — describe review cadence |
| 6.5 | Are service accounts inventoried and monitored? | `scm_list_users` with filter (MCP) — filter service accounts |
| 6.6 | Is just-in-time (JIT) access used for privileged operations? | Manual — describe JIT setup |

### Domain 7: Network Segmentation & Security

**What insurers want to know:** Is the network segmented to limit lateral movement? Are critical systems isolated?

| # | Question | Prelude Data |
|---|----------|--------------|
| 7.1 | Is network segmentation implemented? | Manual — describe architecture |
| 7.2 | Are critical systems (e.g., backup servers, domain controllers) isolated? | Manual — describe isolation |
| 7.3 | Is a next-gen firewall deployed? | `prelude scm network_devices` (CLI only) — network device inventory |
| 7.4 | Is network traffic monitored for anomalies? | Manual — describe NDR/IDS solution |
| 7.5 | Are wireless networks segmented from production? | Manual — describe Wi-Fi architecture |
| 7.6 | Is zero-trust network access (ZTNA) implemented? | Manual — describe ZTNA approach |

### Domain 8: Incident Response

**What insurers want to know:** Is there a documented, tested incident response plan? Is there retainer with an IR firm?

| # | Question | Prelude Data |
|---|----------|--------------|
| 8.1 | Is there a documented incident response plan? | Manual — provide plan reference |
| 8.2 | When was the IR plan last tested (tabletop exercise)? | Manual — provide date and summary |
| 8.3 | Is there an IR retainer with a third-party firm? | Manual — describe retainer |
| 8.4 | Are roles and responsibilities defined for incident response? | Manual — describe RACI |
| 8.5 | Is there a communication plan for cyber incidents? | Manual — describe plan |
| 8.6 | Has the organization experienced a cyber incident in the past 3 years? | Manual — describe history |

### Domain 9: Security Awareness Training

**What insurers want to know:** Are employees trained on cybersecurity risks? Is phishing simulation conducted?

| # | Question | Prelude Data |
|---|----------|--------------|
| 9.1 | Is security awareness training mandatory for all employees? | Manual — describe program |
| 9.2 | How frequently is training conducted? | Manual — describe cadence |
| 9.3 | Are phishing simulations performed? | Manual — describe program |
| 9.4 | What is the phishing simulation click rate? | Manual — provide metrics |
| 9.5 | Is training customized for high-risk roles (finance, executives)? | Manual — describe targeting |

### Domain 10: Security Testing

**What insurers want to know:** Is the organization regularly testing its defenses through penetration testing, red teaming, or continuous validation?

| # | Question | Prelude Data |
|---|----------|--------------|
| 10.1 | Are penetration tests performed regularly? | Manual — describe pen test program |
| 10.2 | Is continuous security validation/testing performed? | `get_activity` with view="protected" (MCP) — protection rates |
| 10.3 | How many security tests are actively running? | `prelude detect queue` (CLI only) — active test queue |
| 10.4 | What is the current overall protection rate? | `get_activity` with view="protected" (MCP) — protection percentage |
| 10.5 | Are test results used to drive remediation? | `get_activity` with view="findings" (MCP) — findings data |
| 10.6 | When was the last external penetration test? | Manual — provide date and firm |

### Domain 11: Logging & Monitoring

**What insurers want to know:** Are security-relevant logs collected, centralized, and monitored with adequate retention?

| # | Question | Prelude Data |
|---|----------|--------------|
| 11.1 | What SIEM/log management solution is used? | `get_account` (MCP) — Splunk/S3 integration status |
| 11.2 | What is the log retention period? | Manual — describe retention policy |
| 11.3 | Is 24/7 security monitoring in place (SOC/MDR)? | Manual — describe monitoring |
| 11.4 | Are alerts triaged and investigated within defined SLAs? | Manual — describe SLA |
| 11.5 | Are authentication logs monitored? | Manual — describe auth monitoring |

### Domain 12: Data Protection & Encryption

**What insurers want to know:** Is sensitive data classified, encrypted, and protected with appropriate controls?

| # | Question | Prelude Data |
|---|----------|--------------|
| 12.1 | Is sensitive data classified and inventoried? | Manual — describe classification |
| 12.2 | Is data encrypted at rest? | `scm_evaluation` (MCP) — encryption policies |
| 12.3 | Is data encrypted in transit (TLS 1.2+)? | Manual — describe TLS configuration |
| 12.4 | Is full disk encryption enabled on all endpoints? | `scm_evaluation` (MCP) — FDE policy compliance |
| 12.5 | Is there a data loss prevention (DLP) solution? | Manual — describe DLP |
| 12.6 | What PII/PHI/PCI data does the organization process? | Manual — describe data types |

## Walkthrough Flow

### For each question, follow this exact flow:

1. **Present**: Show the question and explain why insurers care about it
2. **Prelude check**: If the Prelude Data column lists an MCP tool, offer to pull data:
   - "I can check this from your Prelude data. Want me to query `<tool>`?"
   - If yes, call the MCP tool (or run the CLI command as fallback), summarize findings, and map to the question
3. **External data**: Guide the user on how to provide data from other sources
4. **Collect answer**: Get their response — a description, metric, or "not implemented"
5. **Score**: Assign a readiness rating and ask the user to confirm

### Readiness Ratings

| Rating | Label | Description |
|--------|-------|-------------|
| A | Excellent | Exceeds insurer expectations, documented and tested |
| B | Good | Meets insurer expectations, some documentation gaps |
| C | Adequate | Basic controls in place, improvement needed |
| D | Insufficient | Significant gaps, likely to raise premiums or cause exclusions |
| F | Critical Gap | Missing entirely, may result in coverage denial |

### Pacing

- Process one **domain** at a time
- After each domain, summarize findings and readiness rating
- At the end, generate the full report

## Report Generation

After completing all domains, generate the report:

### Step 1: Build the HTML report

Create an HTML file with embedded CSS using the Prelude brand:

```css
:root {
  --bg: #0f1117;
  --surface: #161922;
  --surface2: #1c2030;
  --border: #2a2e3d;
  --text: #e2e4ea;
  --text-muted: #8b8fa3;
  --accent: #6366f1;
  --accent-light: #818cf8;
  --red: #ef4444;
  --orange: #f59e0b;
  --green: #22c55e;
  --yellow: #eab308;
}
body {
  background: var(--bg);
  color: var(--text);
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  line-height: 1.6;
}
```

The report should include:

1. **Cover page** — Organization name, industry, date, prepared by, Prelude branding
2. **Executive summary** — Overall readiness score, domain-level breakdown, key highlights
3. **Readiness scorecard** — Visual grid showing all 12 domains with A-F ratings and color coding:
   - A/B: green (`#22c55e`)
   - C: yellow (`#eab308`)
   - D: orange (`#f59e0b`)
   - F: red (`#ef4444`)
4. **Domain details** — For each domain:
   - Domain description and overall rating
   - Question-by-question breakdown with: evidence summary, rating, gaps
5. **Risk factors** — Items that could increase premiums or trigger exclusions:
   - Missing MFA (most common exclusion trigger)
   - No EDR on all endpoints
   - No backup testing
   - No IR plan
   - Recent breach history
6. **Insurance readiness summary** — Clear statement of readiness with:
   - Strengths (what will help the application)
   - Gaps (what needs to be addressed)
   - Recommended actions before applying
7. **Prelude data appendix** (if applicable) — Summary of automated data pulls

### Step 2: Convert to PDF

After writing the HTML file, convert to PDF:

```bash
npx --yes puppeteer browsers install chrome 2>/dev/null
node -e "
const puppeteer = require('puppeteer');
(async () => {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();
  await page.goto('file://<absolute_path_to_html>', {waitUntil: 'networkidle0'});
  await page.pdf({path: '<output_path>.pdf', format: 'A4', printBackground: true, margin: {top: '20mm', bottom: '20mm', left: '15mm', right: '15mm'}});
  await browser.close();
})();
"
```

If puppeteer is not available or fails, try alternatives in order:
1. `wkhtmltopdf <html_file> <output.pdf>`
2. `python3 -c "from weasyprint import HTML; HTML(filename='<html_file>').write_pdf('<output.pdf>')"`
3. If none work, inform the user the HTML report is ready and they can print to PDF from their browser (Cmd+P / Ctrl+P → Save as PDF)

### Step 3: Deliver

Tell the user where the PDF (or HTML) was saved and highlight:
- Their overall readiness rating
- The top 3 items to address before their insurance application/renewal
- Any items that could trigger coverage exclusions
