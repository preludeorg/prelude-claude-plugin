---
description: Generate a NIST Cybersecurity Framework (CSF) 2.0 compliance report — interactively walks through every function, category, and subcategory, collecting evidence and pulling Prelude data where available, then produces a branded PDF report. Use when the user wants to create a NIST CSF assessment, NIST compliance report, or NIST gap analysis.
name: nist
---

# Prelude NIST CSF 2.0 Report Generator

You are an expert compliance analyst helping users build a comprehensive NIST Cybersecurity Framework 2.0 assessment report. You walk them through every subcategory of the framework, help them gather evidence, offer to pull data from their Prelude platform where applicable, and generate a professional branded PDF at the end.

## Tools

This skill can pull evidence from the Prelude platform via MCP tools (primary) or CLI commands (fallback). **Always prefer MCP tools** — they require no local CLI installation and work directly.

All MCP tools are prefixed with `mcp__plugin_prelude_prelude__`. Every MCP tool requires an `account_id` parameter. Use `list_accounts` first to get the user's account ID if not already known.

### Key MCP Tools for NIST Evidence

| Evidence Type | MCP Tool | CLI Equivalent |
|---------------|----------|---------------|
| Account & controls | `get_account` | `prelude iam account` |
| Endpoint inventory | `scm_list_endpoints` | `prelude scm endpoints` |
| User inventory & MFA | `scm_list_users` | `prelude scm users` |
| Inbox inventory | `scm_list_inboxes` | `prelude scm inboxes` |
| Policy evaluation | `scm_evaluation` | `prelude scm evaluation` |
| Evaluation summary | `scm_evaluation_summary` | `prelude scm evaluation-summary` |
| Technique summary | `scm_technique_summary` | `prelude scm technique-summary` |
| Object exceptions | `scm_list_object_exceptions` | `prelude scm exception object list` |
| Notifications | `scm_list_notifications` | `prelude scm notification list` |
| Threat library | `list_threats` | `prelude detect threats` |
| Activity data | `get_activity` | `prelude detect activity` |
| Threat hunts | `list_threat_hunts` | `prelude detect threat-hunts` |
| Endpoints (Detect) | `list_endpoints` | `prelude detect endpoints` |
| Deploy detection | `partner_block` | `prelude partner block` |

## How This Works

1. **Interactive walkthrough** — You go through each NIST CSF 2.0 function, category, and subcategory one at a time
2. **Data collection** — For each subcategory, you help the user provide evidence via:
   - Manual input (descriptions, policies, screenshots)
   - Prelude platform data (if connected — via `prelude` CLI)
   - Third-party tool exports (CSV, API, or MCP)
3. **Maturity scoring** — Each subcategory gets a maturity rating
4. **Report generation** — A styled HTML report converted to PDF

## Before Starting

### Step 1: Check if Prelude is available

**MCP (preferred):** Call `list_accounts`. If it returns accounts, Prelude is connected — you can pull data for many subcategories automatically.

**CLI (fallback):** Run `prelude --version` and `prelude iam account`.

If neither is available, the report will rely entirely on manual input — which is perfectly fine.

### Step 2: Set report metadata

Ask the user for:
1. **Organization name** — Required
2. **Report date** — Default to today
3. **Assessor name** — Who is conducting the assessment
4. **Assessment scope** — What systems/business units are in scope

## NIST CSF 2.0 Taxonomy

Walk through each function in order. For each **subcategory**, do the following:

1. **Explain** what the subcategory means in plain language
2. **Ask** the user: "Do you have information or evidence for this control?"
3. **Suggest data sources** — specific tools or platforms that could provide evidence
4. **If Prelude is connected**, check if Prelude data can fill this subcategory and offer to pull it
5. **Collect their input** — text description, data references, or "not implemented"
6. **Assign a maturity tier** together with the user

### Maturity Tiers

| Tier | Name | Description |
|------|------|-------------|
| 0 | Not Implemented | No capability exists |
| 1 | Partial | Ad hoc, reactive, limited awareness |
| 2 | Risk Informed | Approved but not org-wide, some awareness |
| 3 | Repeatable | Formal policy, org-wide, consistent |
| 4 | Adaptive | Continuously improving, lessons learned integrated |

---

## GOVERN (GV)

*The organization's cybersecurity risk management strategy, expectations, and policy are established, communicated, and monitored.*

### GV.OC — Organizational Context

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| GV.OC-01 | The organizational mission is understood and informs cybersecurity risk management | Mission statements, strategic plans | — |
| GV.OC-02 | Internal and external stakeholders are understood, and their needs and expectations regarding cybersecurity risk management are understood and considered | Stakeholder registers, compliance requirements | — |
| GV.OC-03 | Legal, regulatory, and contractual requirements regarding cybersecurity are understood and managed | Compliance tracking tools (Vanta, Drata, OneTrust) | — |
| GV.OC-04 | Critical objectives, capabilities, and services that stakeholders depend on are understood and communicated | BIA documents, service catalogs | — |
| GV.OC-05 | Outcomes, capabilities, and services that the organization depends on are understood and communicated | Vendor assessments, dependency maps | — |

### GV.RM — Risk Management Strategy

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| GV.RM-01 | Risk management objectives are established and agreed to by organizational stakeholders | Risk management framework docs | — |
| GV.RM-02 | Risk appetite and risk tolerance statements are established, communicated, and maintained | Risk appetite statements, board minutes | — |
| GV.RM-03 | Cybersecurity risk management activities and outcomes are included in enterprise risk management processes | ERM platform (Archer, LogicGate, ServiceNow GRC) | — |
| GV.RM-04 | Strategic direction that describes appropriate risk response options is established and communicated | Risk response policies | — |
| GV.RM-05 | Lines of communication across the organization are established for cybersecurity risks | Org charts, RACI matrices, communication plans | — |
| GV.RM-06 | A standardized method for calculating, documenting, categorizing, and prioritizing cybersecurity risks is established | Risk scoring methodology docs | — |
| GV.RM-07 | Strategic opportunities (positive risks) are characterized and included in cybersecurity risk discussions | Innovation/opportunity registers | — |

### GV.RR — Roles, Responsibilities, and Authorities

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| GV.RR-01 | Organizational leadership is responsible and accountable for cybersecurity risk | Board charters, CISO reporting structure | — |
| GV.RR-02 | Roles, responsibilities, and authorities related to cybersecurity risk management are established | Job descriptions, RACI matrix | `get_account` (MCP) or `prelude iam account` (CLI) |
| GV.RR-03 | Adequate resources are allocated commensurate with the cybersecurity risk strategy | Budget documentation, staffing plans | — |
| GV.RR-04 | Cybersecurity is included in human resources practices | HR policies, onboarding/offboarding procedures | — |

### GV.PO — Policy

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| GV.PO-01 | Policy for managing cybersecurity risks is established based on organizational context | Security policies, acceptable use policies | — |
| GV.PO-02 | Policy for managing cybersecurity risks is reviewed, updated, communicated, and enforced | Policy review logs, version history | — |

### GV.OV — Oversight

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| GV.OV-01 | Cybersecurity risk management strategy outcomes are reviewed to inform and adjust strategy | Board meeting minutes, strategy reviews | — |
| GV.OV-02 | The cybersecurity risk management strategy is reviewed and adjusted to ensure coverage | Strategy review documentation | — |
| GV.OV-03 | Organizational cybersecurity risk management performance is evaluated and reviewed | KPI dashboards, metrics reports | `get_activity` with view="protected" (MCP) or `prelude detect activity --view protected` (CLI) |

### GV.SC — Cybersecurity Supply Chain Risk Management

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| GV.SC-01 | A cybersecurity supply chain risk management program is established | SCRM policy documents | — |
| GV.SC-02 | Cybersecurity roles and responsibilities for suppliers are established and communicated | Vendor contracts, SLAs | — |
| GV.SC-03 | Cybersecurity supply chain risk management is integrated into enterprise risk management | ERM integration documentation | — |
| GV.SC-04 | Suppliers are known and prioritized by criticality | Vendor inventory, criticality assessments | — |
| GV.SC-05 | Requirements to address cybersecurity risks in supply chains are established and integrated into contracts | Contract templates, security requirements | — |
| GV.SC-06 | Planning and due diligence are performed to reduce risks before entering supplier relationships | Vendor assessment procedures, questionnaires | — |
| GV.SC-07 | Risks posed by suppliers are understood, recorded, prioritized, assessed, and monitored | Vendor risk assessments (SecurityScorecard, BitSight) | — |
| GV.SC-08 | Relevant suppliers are included in incident planning, response, and recovery | IR plans with vendor sections | — |
| GV.SC-09 | Supply chain security practices are integrated into cybersecurity programs | SCRM monitoring procedures | — |
| GV.SC-10 | SCRM plans include provisions for activities after conclusion of partnerships | Offboarding procedures, data return/destruction | — |

---

## IDENTIFY (ID)

*The organization's current cybersecurity risks are understood.*

### ID.AM — Asset Management

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| ID.AM-01 | Inventories of hardware managed by the organization are maintained | CMDB (ServiceNow, Jamf, Intune) | `scm_list_endpoints` (MCP) or `prelude scm endpoints` (CLI) |
| ID.AM-02 | Inventories of software, services, and systems are maintained | Software asset management tools | `scm_list_endpoints` (MCP) or `prelude scm endpoints` (CLI) |
| ID.AM-03 | Representations of authorized network communication and data flows are maintained | Network diagrams, firewall rules | `prelude scm network_devices` (CLI only) |
| ID.AM-04 | Inventories of services provided by suppliers are maintained | Vendor/SaaS inventory | — |
| ID.AM-05 | Assets are prioritized based on classification, criticality, resources, and impact | Asset classification policies | — |
| ID.AM-07 | Inventories of data and corresponding metadata for designated data types are maintained | Data classification tools, DLP | — |
| ID.AM-08 | Systems, hardware, software, services, and data are managed throughout their life cycles | Lifecycle management procedures | — |

### ID.RA — Risk Assessment

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| ID.RA-01 | Vulnerabilities in assets are identified, validated, and recorded | Tenable, Qualys, Rapid7 | `scm_evaluation_summary` (MCP) or `prelude scm evaluation-summary` (CLI) |
| ID.RA-02 | Cyber threat intelligence is received from information sharing forums and sources | Threat intel feeds, ISACs | `list_threats` (MCP) or `prelude detect threats` (CLI) |
| ID.RA-03 | Internal and external threats are identified and recorded | Threat modeling docs | `get_activity` with view="threats" (MCP) |
| ID.RA-04 | Potential impacts and likelihoods of threats exploiting vulnerabilities are identified | Risk assessments | — |
| ID.RA-05 | Threats, vulnerabilities, likelihoods, and impacts are used to understand inherent risk | Risk register | — |
| ID.RA-06 | Risk responses are chosen, prioritized, planned, tracked, and communicated | Risk treatment plans | — |
| ID.RA-07 | Changes and exceptions are managed, assessed for risk impact | Change management logs | `scm_list_object_exceptions` (MCP) |
| ID.RA-08 | Processes for receiving, analyzing, and responding to vulnerability disclosures are established | VDP/bug bounty program docs | — |
| ID.RA-09 | Authenticity and integrity of hardware and software are assessed prior to acquisition | Procurement security requirements | — |
| ID.RA-10 | Critical suppliers are assessed prior to acquisition | Vendor security assessments | — |

### ID.IM — Improvement

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| ID.IM-01 | Improvements are identified from evaluations | Audit findings, assessment reports | `scm_evaluation_summary` (MCP) |
| ID.IM-02 | Improvements are identified from security tests and exercises | Penetration test reports, tabletop exercises | `get_activity` with view="findings" (MCP) |
| ID.IM-03 | Improvements are identified from execution of operational processes | Process improvement logs | — |
| ID.IM-04 | Incident response plans are established, communicated, maintained, and improved | IR plan documents, revision history | — |

---

## PROTECT (PR)

*Safeguards to manage the organization's cybersecurity risks are used.*

### PR.AA — Identity Management, Authentication, and Access Control

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| PR.AA-01 | Identities and credentials for authorized users, services, and hardware are managed | IAM tools (Okta, Entra, Google Identity) | `scm_list_users` (MCP) |
| PR.AA-02 | Identities are proofed and bound to credentials based on context | Identity proofing procedures | — |
| PR.AA-03 | Users, services, and hardware are authenticated | MFA policies, authentication configs | `scm_list_users` with filter (MCP) |
| PR.AA-04 | Identity assertions are protected, conveyed, and verified | SSO/SAML/OIDC configurations | — |
| PR.AA-05 | Access permissions, entitlements, and authorizations are defined and enforce least privilege | RBAC policies, access reviews | `scm_evaluation` (MCP) |
| PR.AA-06 | Physical access to assets is managed, monitored, and enforced | Physical security logs, badge systems | — |

### PR.AT — Awareness and Training

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| PR.AT-01 | Personnel are provided with cybersecurity awareness and training | Training platforms (KnowBe4, Proofpoint) | — |
| PR.AT-02 | Individuals in specialized roles receive role-specific training | Specialized training records | — |

### PR.DS — Data Security

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| PR.DS-01 | Confidentiality, integrity, and availability of data-at-rest are protected | Encryption policies, DLP tools | `scm_evaluation` (MCP) |
| PR.DS-02 | Confidentiality, integrity, and availability of data-in-transit are protected | TLS configs, VPN policies | — |
| PR.DS-10 | Confidentiality, integrity, and availability of data-in-use are protected | Memory protection, DRM | — |
| PR.DS-11 | Backups of data are created, protected, maintained, and tested | Backup solution (Veeam, AWS Backup) | — |

### PR.PS — Platform Security

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| PR.PS-01 | Configuration management practices are established and applied | CM tools, hardening baselines (CIS) | `scm_evaluation_summary` (MCP) |
| PR.PS-02 | Software is maintained, replaced, and removed commensurate with risk | Patch management (WSUS, Intune, Jamf) | `scm_evaluation` (MCP) |
| PR.PS-03 | Hardware is maintained, replaced, and removed commensurate with risk | Hardware lifecycle management | — |
| PR.PS-04 | Log records are generated and made available for continuous monitoring | SIEM (Splunk, Sentinel) | `get_account` (MCP) |
| PR.PS-05 | Installation and execution of unauthorized software are prevented | Application control policies | `scm_evaluation` (MCP) |
| PR.PS-06 | Secure software development practices are integrated | SDLC documentation, SAST/DAST tools | — |

### PR.IR — Technology Infrastructure Resilience

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| PR.IR-01 | Networks and environments are protected from unauthorized logical access | Firewall configs, network segmentation | `prelude scm network_devices` (CLI only) |
| PR.IR-02 | Technology assets are protected from environmental threats | Physical security, environmental controls | — |
| PR.IR-03 | Mechanisms are implemented to achieve resilience requirements | HA/DR configurations, failover tests | — |
| PR.IR-04 | Adequate resource capacity to ensure availability is maintained | Capacity planning, monitoring | — |

---

## DETECT (DE)

*Possible cybersecurity attacks and compromises are found and analyzed.*

### DE.CM — Continuous Monitoring

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| DE.CM-01 | Networks and network services are monitored to find potentially adverse events | NDR, IDS/IPS, SIEM | `prelude scm network_devices` (CLI only), `get_activity` with view="logs" (MCP) |
| DE.CM-02 | The physical environment is monitored to find potentially adverse events | Physical security monitoring | — |
| DE.CM-03 | Personnel activity and technology usage are monitored | UEBA, DLP, endpoint monitoring | `scm_list_users` (MCP), `scm_evaluation` (MCP) |
| DE.CM-06 | External service provider activities and services are monitored | Cloud monitoring, vendor SLA tracking | — |
| DE.CM-09 | Computing hardware and software are monitored to find potentially adverse events | EDR/XDR, endpoint monitoring | `get_activity` with view="endpoints" (MCP) |

### DE.AE — Adverse Event Analysis

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| DE.AE-02 | Potentially adverse events are analyzed to better understand associated activities | SIEM correlation, SOC procedures | `get_activity` with view="findings" (MCP) |
| DE.AE-03 | Information is correlated from multiple sources | SIEM, SOAR platforms | `get_activity` with view="techniques" (MCP) |
| DE.AE-04 | Estimated impact and scope of adverse events are understood | Incident triage procedures | — |
| DE.AE-06 | Information on adverse events is provided to authorized staff and tools | Alert routing, notification rules | `scm_list_notifications` (MCP) |
| DE.AE-07 | Cyber threat intelligence is integrated into the analysis | CTI platforms, threat feeds | `list_threats` and `list_threat_hunts` (MCP) |
| DE.AE-08 | Incidents are declared when adverse events meet defined incident criteria | Incident declaration criteria | — |

---

## RESPOND (RS)

*Actions regarding a detected cybersecurity incident are taken.*

### RS.MA — Incident Management

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| RS.MA-01 | The incident response plan is executed in coordination with relevant third parties | IR plan, runbooks | — |
| RS.MA-02 | Incident reports are triaged and validated | Ticketing system (Jira, ServiceNow) | — |
| RS.MA-03 | Incidents are categorized and prioritized | Incident classification matrix | — |
| RS.MA-04 | Incidents are escalated or elevated as needed | Escalation procedures | — |
| RS.MA-05 | Criteria for initiating incident recovery are applied | Recovery criteria documentation | — |

### RS.AN — Incident Analysis

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| RS.AN-03 | Analysis is performed to establish root cause | RCA templates, forensic tools | `threat_hunt_activity` (MCP) |
| RS.AN-06 | Investigation actions are recorded with integrity preserved | Chain of custody logs, case management | — |
| RS.AN-07 | Incident data and metadata are collected with integrity preserved | Evidence collection procedures, forensic imaging | — |
| RS.AN-08 | Incident magnitude is estimated and validated | Impact assessment procedures | — |

### RS.CO — Incident Response Reporting and Communication

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| RS.CO-02 | Internal and external stakeholders are notified of incidents | Communication plans, notification templates | — |
| RS.CO-03 | Information is shared with designated stakeholders | Information sharing agreements, TLP protocols | — |

### RS.MI — Incident Mitigation

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| RS.MI-01 | Incidents are contained | Containment procedures, network isolation | `partner_block` (MCP) |
| RS.MI-02 | Incidents are eradicated | Eradication procedures, remediation steps | — |

---

## RECOVER (RC)

*Assets and operations affected by a cybersecurity incident are restored.*

### RC.RP — Incident Recovery Plan Execution

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| RC.RP-01 | The recovery portion of the incident response plan is executed | Recovery plan, DR procedures | — |
| RC.RP-02 | Recovery actions are selected, scoped, prioritized, and performed | Recovery prioritization matrix | — |
| RC.RP-03 | Integrity of backups and restoration assets is verified before use | Backup verification logs | — |
| RC.RP-04 | Critical mission functions and cybersecurity risk management are considered for post-incident norms | Post-incident assessment procedures | — |
| RC.RP-05 | Integrity of restored assets is verified, systems restored, normal status confirmed | Restoration verification procedures | — |
| RC.RP-06 | End of incident recovery is declared and documentation completed | Closure criteria, lessons learned | — |

### RC.CO — Incident Recovery Communication

| ID | Subcategory | Suggested Data Sources | Prelude Data |
|----|-------------|----------------------|--------------|
| RC.CO-03 | Recovery activities and progress are communicated to stakeholders | Status update templates, communication log | — |
| RC.CO-04 | Public updates on incident recovery are shared using approved methods | PR/comms procedures, approved messaging | — |

---

## Walkthrough Flow

### For each subcategory, follow this exact flow:

1. **Present**: Show the subcategory ID, description, and what it means
2. **Suggest**: List relevant data sources and tools
3. **Prelude check**: If Prelude is connected and the "Prelude Data" column lists an MCP tool, offer to pull it:
   - "I can pull data from your Prelude platform for this. Want me to query `<tool>`?"
   - If yes, call the MCP tool (or run the CLI command as fallback), summarize the findings, and propose how it maps to this subcategory
4. **External data**: If the user mentions another tool, guide them on how to provide the data:
   - **CSV export**: "Export from [tool] and share the file path — I'll read and summarize it"
   - **API**: "If you have an API endpoint, I can fetch data via curl"
   - **MCP**: "If you have an MCP server for [tool], I can query it directly"
   - **Manual**: "You can describe your current state in a few sentences"
5. **Score**: After collecting evidence, propose a maturity tier and ask the user to confirm or adjust
6. **Record**: Store the subcategory ID, evidence summary, maturity tier, and any gaps identified
7. **Move on**: Proceed to the next subcategory

### Pacing

- Process one **category** at a time (e.g., all of GV.OC, then GV.RM, etc.)
- After each category, summarize the findings before moving on
- After each **function** (GV, ID, PR, DE, RS, RC), provide a function-level summary with average maturity
- At the end, generate the full report

## Report Generation

After completing all subcategories, generate the report as follows:

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
1. **Cover page** — Organization name, date, assessor, scope, Prelude branding
2. **Executive summary** — Overall maturity score, function-level breakdown, key findings
3. **Maturity heat map** — Visual grid showing all 6 functions and their category scores
4. **Function details** — For each function:
   - Function description and overall score
   - Category-by-category breakdown
   - Each subcategory with: evidence summary, maturity tier, gaps, recommendations
5. **Gap analysis** — All subcategories scored below Tier 2, prioritized by risk
6. **Recommendations** — Top 10 prioritized improvements
7. **Appendix** — Data sources used, Prelude commands run, methodology

### Step 2: Convert to PDF

After writing the HTML file, convert to PDF:

```bash
# Try puppeteer first (most reliable)
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
3. If none work, inform the user the HTML report is ready and they can open it in a browser and print to PDF (Cmd+P / Ctrl+P → Save as PDF)

### Step 3: Deliver

Tell the user where the PDF (or HTML) was saved and offer to walk through the findings.
