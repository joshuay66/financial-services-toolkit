# Adopting Cursor AI in a Credit Union: A Compliance, Security & Configuration Guide

> **A practical framework for credit unions evaluating Cursor as an enterprise AI coding tool — addressing NCUA, FFIEC, PCI-DSS, and emerging federal AI guidance.**

---

## Why This Guide Exists

Credit unions are under increasing pressure to modernize their technology capabilities while operating within one of the most heavily regulated environments in financial services. AI-powered developer tools like [Cursor](https://cursor.com/enterprise) can dramatically accelerate software delivery — but adopting them in a federally insured credit union requires more than just signing an enterprise agreement.

This guide provides a structured approach to evaluating and deploying Cursor Enterprise in a credit union environment. It covers the regulatory landscape, security architecture review, required configurations, risk assessment considerations, and a phased rollout framework. While written specifically for Cursor, much of the compliance and governance approach applies to any AI-assisted development tool.

**This guide is current as of March 2026.** Regulatory guidance in this space is evolving rapidly. Always validate against the latest NCUA, FFIEC, and NIST publications.

---

## Table of Contents

- [1. What Is Cursor?](#1-what-is-cursor)
- [2. The Regulatory Landscape](#2-the-regulatory-landscape)
- [3. Cursor's Enterprise Security Architecture](#3-cursors-enterprise-security-architecture)
- [4. Data Flow & Subprocessors](#4-data-flow--subprocessors)
- [5. Mandatory Day-One Configurations](#5-mandatory-day-one-configurations)
- [6. Data Classification Rules](#6-data-classification-rules)
- [7. Risk Assessment Framework](#7-risk-assessment-framework)
- [8. Third-Party Risk Management & Due Diligence](#8-third-party-risk-management--due-diligence)
- [9. Secure SDLC Integration](#9-secure-sdlc-integration)
- [10. Phased Rollout Plan](#10-phased-rollout-plan)
- [11. Examiner Readiness](#11-examiner-readiness)
- [12. Key Risks to Accept (Eyes Wide Open)](#12-key-risks-to-accept-eyes-wide-open)
- [13. Quick-Reference Checklist](#13-quick-reference-checklist)
- [Resources & References](#resources--references)
- [Contributing](#contributing)

---

## 1. What Is Cursor?

[Cursor](https://cursor.com) is an AI-powered code editor built as a fork of Visual Studio Code, developed by Anysphere, Inc. It integrates frontier large language models (from Anthropic, OpenAI, Google, and xAI) directly into the development workflow.

**Core capabilities include:**

- **AI Agent (Composer)** — Autonomous multi-file code generation and refactoring
- **Cursor Tab** — Keystroke-level intelligent code completion
- **Bugbot** — Automated code review on pull requests with policy and security checks
- **Codebase Indexing** — Semantic search across your entire repository
- **Chat** — Contextual Q&A against your codebase
- **CLI** — Terminal-based agentic coding

**Enterprise traction:** As of early 2026, Cursor reports adoption across 64% of Fortune 500 companies and over 50,000 enterprises, including NVIDIA, Stripe, Coinbase, Samsung, PwC, Deloitte, and Optiver. They raised a $2.3B Series D and report over $1B in annualized revenue.

**Why it matters for credit unions:** If your credit union builds or maintains any custom software — whether that's internal tooling, integrations with your core (Symitar, Corelation, Fiserv DNA, etc.), digital banking customizations, or fraud detection systems — Cursor can meaningfully accelerate your development team. But the regulatory bar is higher for us than for a typical tech company.

---

## 2. The Regulatory Landscape

### NCUA

The NCUA published an [updated AI resource hub](https://ncua.gov/regulation-supervision/regulatory-compliance-resources/artificial-intelligence-ai) in December 2025 (updated January 2026), explicitly linking AI oversight to existing third-party relationship guidance:

- **Letter 07-CU-13** — Evaluating Third Party Relationships
- **Letter 01-CU-20** — Due Diligence Over Third Party Service Providers

The NCUA emphasizes that AI systems require consideration *beyond* traditional vendor management, citing risks around algorithmic opacity, fair lending, data privacy, operational resilience, and model risk.

**Important context:** A [May 2025 GAO report](https://www.gao.gov/assets/gao-25-107197.pdf) found that NCUA's model risk management guidance is limited in scope (last updated 2016, covering only interest rate risk models) and recommended updates. GAO also recommended Congress grant NCUA authority to examine third-party technology service providers. Expect examiner expectations to evolve — likely toward the NIST AI RMF and Treasury FS AI RMF as benchmarks.

### FFIEC

The [FFIEC IT Examination Handbook](https://ithandbook.ffiec.gov/) provides the examination procedures your examiners will follow. Relevant booklets:

| Booklet | What Examiners Will Look For |
|---|---|
| **Information Security** | Security program adequacy, access controls, data protection |
| **Architecture, Infrastructure & Operations** | Technology risk management, infrastructure controls |
| **Development & Acquisition** | Software development controls, vendor procurement |
| **Outsourcing Technology Services** | Third-party risk management, due diligence, contracts |

### PCI-DSS

If developers use Cursor in environments that touch cardholder data or connect to the cardholder data environment (CDE), PCI-DSS applies directly. Key requirements:

| Requirement | Applicability to Cursor |
|---|---|
| **Req. 3** — Protect stored account data | No cardholder data should ever be transmitted to Cursor's servers |
| **Req. 4** — Strong cryptography in transit | Cursor uses TLS 1.2+ ✓ |
| **Req. 6** — Secure systems and software | AI-generated code must go through your existing secure SDLC |
| **Req. 7 & 8** — Restrict & identify access | SSO/SCIM integration and role-based access |
| **Req. 10** — Log and monitor | Usage analytics and audit logging |
| **Req. 12.8** — Manage service providers | Formal agreement and monitoring program |

### Treasury FS AI RMF (February 2026)

On February 19, 2026, the U.S. Treasury released the [Financial Services AI Risk Management Framework](https://home.treasury.gov/news/press-releases/sb0401) and an AI Lexicon. Key points:

- **Non-binding but influential** — expect examiners to reference this as a benchmark
- **Sector-specific adaptation of the NIST AI RMF** — built with input from 100+ financial institutions
- **Four components:** AI Adoption Stage Questionnaire, Risk and Control Matrix, Implementation Guidebook, and Control Objective Reference Guide
- **Contractual guidance:** recommends visibility into vendor model and data practices, audit and testing rights, update and change controls, and incident response coordination

### NIST AI Cybersecurity Profile

NIST released a preliminary draft AI Cybersecurity Risk Profile in December 2025, built on the NIST CSF 2.0. This provides a structured approach to AI-specific cybersecurity risks and is designed to complement existing risk management frameworks. A full initial public draft is expected later in 2026.

---

## 3. Cursor's Enterprise Security Architecture

This section is based on Cursor's [Security page](https://www.cursor.com/security) (last updated January 27, 2026) and [Enterprise page](https://cursor.com/enterprise).

| Security Feature | Description | Regulatory Relevance |
|---|---|---|
| **Privacy Mode (Zero Data Retention)** | When enabled, code data is never stored by Cursor or its LLM providers and never used for model training. Dual-replica architecture separates privacy-mode traffic. Enforced by default for team members. | FFIEC Data Security; PCI-DSS Req. 3 & 7; GLBA |
| **SOC 2 Type II** | Annual SOC 2 audit; report available via [trust.cursor.com](https://trust.cursor.com). Annual third-party pen testing. | FFIEC IT Examination; NCUA 07-CU-13; PCI-DSS 12.8 |
| **AES-256 at Rest / TLS 1.2+ in Transit** | Industry-standard encryption for stored and transmitted data | PCI-DSS Req. 3.4, 4.1; FFIEC InfoSec |
| **SAML SSO & SCIM** | SAML-based single sign-on. SCIM for automated user provisioning/deprovisioning. | FFIEC Access Controls; PCI-DSS Req. 8 |
| **Centralized Admin Controls** | Global configuration of model access, MCP controls, system-level agent rules, model blocklists | FFIEC IT Governance; Change Management |
| **Codebase Indexing Controls** | Can be disabled or scoped via `.cursorignore`. File paths obfuscated with client-side encryption. No plaintext code persisted for privacy-mode users. | PCI-DSS Req. 3; Data Minimization |
| **Usage Analytics Dashboard** | Tracks agent requests, tab completions, per-user activity, model usage | FFIEC IT Audit; Monitoring & Logging |
| **GDPR & CCPA Compliance** | Built-in compliance. Account deletion with 30-day data purge guarantee. | Consumer Data Protection |

---

## 4. Data Flow & Subprocessors

Understanding where code data flows is critical for your regulatory posture.

### How AI Requests Work

When you use any Cursor AI feature (chat, Tab, agent, Bugbot), the request flows:

```
Developer's Machine → Cursor's AWS Infrastructure (US) → LLM Inference Provider
```

All requests transit Cursor's AWS servers, even if you configure your own API key. Prompt construction happens server-side. **There is currently no self-hosted or on-premises option, and no ability to direct-route to your own cloud AI deployment.**

### Key Subprocessors (Privacy Mode)

| Provider | Role | Data Retention (Privacy Mode) |
|---|---|---|
| **AWS** | Primary infrastructure (US) | Sees code data; no persistent storage in Privacy Mode |
| **Cloudflare** | Reverse proxy | Sees code data (transit only) |
| **Azure / GCP** | Secondary infrastructure (US) | Sees code data |
| **Fireworks, Baseten, Together** | Custom model hosting (US/Canada/Europe/Japan) | Zero data retention agreement |
| **OpenAI** | LLM provider | Zero data retention agreement |
| **Anthropic** | LLM provider | Zero data retention agreement |
| **Google Cloud Vertex** | Gemini models | Zero data retention agreement |
| **xAI** | Grok models | Zero data retention agreement |
| **Turbopuffer** | Codebase index embeddings (GCP, US) | Stores obfuscated embeddings only |

**Key facts:**
- No infrastructure in China; no Chinese subprocessors (direct or indirect, per Cursor's disclosure)
- Privacy Mode is enforced at the infrastructure level via parallel service replicas — it's not just a flag
- Over 50% of all Cursor users have Privacy Mode enabled
- Background requests may be sent to providers (OpenAI, Anthropic, Google) for summarization tasks with zero data retention, regardless of which model you've selected — but model blocklists are respected

### How Codebase Indexing Works

When enabled, Cursor computes a Merkle tree of file hashes, uploads changed files, chunks and embeds them, and stores embeddings in Turbopuffer. For privacy-mode users, no plaintext code is stored — only obfuscated file paths and embeddings. However, academic research has shown that embedding reversal is theoretically possible, which represents a residual risk for highly sensitive codebases.

**Controls available:**
- Disable indexing entirely in settings
- Use `.cursorignore` to exclude specific files and directories
- `.gitignore` patterns are also respected

---

## 5. Mandatory Day-One Configurations

These configurations should be in place **before any developer starts using Cursor**.

### 1. Enforce Privacy Mode (Organization-Wide)

This is the single most important control. Enable it at the team/organization level via admin settings. It activates zero data retention across all subprocessors and routes all traffic through dedicated privacy-mode infrastructure.

### 2. Configure SAML SSO

Integrate with your identity provider (Azure AD, Okta, etc.) via `adminportal42.cursor.sh`. Enforce MFA at the IdP level. All Cursor authentication should flow through your existing identity governance.

### 3. Enable SCIM Provisioning

Automate user provisioning and deprovisioning from your directory. Terminated employees lose Cursor access without manual intervention.

### 4. Restrict Model Access

Use admin controls to limit which LLM providers are available. Block any models not approved through your vendor assessment. Document the approved list and rationale.

### 5. Lock Down MCP Controls

Restrict or disable Model Context Protocol server connections to prevent unauthorized external integrations. Only whitelist approved MCPs.

### 6. Configure Network Allowlisting

Allowlist only the required Cursor domains in your corporate proxy/firewall:

```
api2.cursor.sh          # Most API requests
api3.cursor.sh          # Cursor Tab (HTTP/2)
api4.cursor.sh          # Cursor Tab by region (HTTP/2)
api5.cursor.sh          # Agent requests
repo42.cursor.sh        # Codebase indexing (HTTP/2)
adminportal42.cursor.sh # SSO and domain verification

# Regional Tab endpoints
us-asia.gcpp.cursor.sh
us-eu.gcpp.cursor.sh
us-only.gcpp.cursor.sh

# Updates and extensions
marketplace.cursorapi.com
cursor-cdn.com
downloads.cursor.com
anysphere-binaries.s3.us-east-1.amazonaws.com
```

### 7. Deploy a Standard `.cursorignore`

Create a `.cursorignore` template that excludes at minimum:

```gitignore
# Encryption keys and certificates
*.pem
*.key
*.p12
*.pfx
*.jks
*.keystore

# Environment and credential files
.env
.env.*
**/secrets/
**/credentials/

# PCI-scoped files
**/cardholder*/
**/pan_*/
**/cvv*/

# Member PII data files
**/member_data/
**/pii/
**/ssn*/

# Configuration with sensitive values
**/config/production*
**/vault/
```

Distribute this as a mandatory repository template and validate enforcement during code review.

### 8. Enable Workspace Trust

Cursor disables VS Code's Workspace Trust by default. Re-enable it:

```json
{
  "security.workspace.trust.enabled": true
}
```

### 9. Maintain an Approved Extension List

Cursor does not verify extension signatures (unlike recent VS Code). Until this is supported, maintain an approved extension list and restrict unapproved installs through policy and endpoint management.

---

## 6. Data Classification Rules

| Classification | Examples | Use with Cursor? | Required Control |
|---|---|---|---|
| 🔴 **Restricted** | PANs, CVVs, encryption keys, member SSNs, passwords, auth tokens | **NO — Never** | Must be in `.cursorignore`; never pasted into chat |
| 🟡 **Confidential** | Internal source code, business logic, API schemas, internal docs | **Yes — with Privacy Mode** | Privacy Mode enforced; `.cursorignore` for sensitive configs |
| 🔵 **Internal** | General application code, test code, IaC, build scripts | **Yes — with Privacy Mode** | Privacy Mode enforced; standard code review |
| 🟢 **Public** | Open-source code, public documentation, public-facing UI code | **Yes** | Standard code review |

**The golden rule:** If you wouldn't email it to a vendor in plaintext, don't paste it into Cursor's chat or let it be included in an AI request.

---

## 7. Risk Assessment Framework

Use this as a starting point for your own risk assessment. Adjust inherent and residual risk ratings based on your institution's size, complexity, and risk appetite.

| Risk Domain | Inherent Risk | Cursor's Mitigation | Your Compensating Control | Residual Risk |
|---|---|---|---|---|
| Source Code Exfiltration | High | Privacy Mode; TLS 1.2+; AES-256 | `.cursorignore`; network monitoring; DLP | Medium |
| Cardholder Data Exposure | Critical | Privacy Mode; no persistent storage | Prohibit in CDE; `.cursorignore`; segmentation | Low |
| Third-Party Concentration | High | 12+ subprocessors with ZDR agreements | Contractual audit rights; annual SOC 2 review; exit plan | Medium |
| Insecure AI-Generated Code | High | Bugbot automated review | Mandatory human review; SAST/DAST in CI/CD | Medium-Low |
| Unauthorized Access | Medium | SAML SSO; SCIM; admin controls | MFA via IdP; RBAC; quarterly access reviews | Low |
| Prompt Injection / Model Manipulation | Medium | Model blocklists; centralized rules | Developer training; code review; sandbox testing | Medium |
| Vendor Lock-in / Availability | Medium | VS Code fork (portable extensions) | Portability plan; VS Code fallback; SLA in contract | Low |
| Regulatory Non-Compliance | High | SOC 2; GDPR/CCPA | Adoption plan; ongoing monitoring; examiner prep | Low |
| Embedding Reversal | Low-Medium | File path obfuscation; client-side encryption | Disable indexing for sensitive repos | Low |
| Extension Supply Chain | Medium | VS Code fork; marketplace | Workspace Trust; approved extension list | Low-Medium |

---

## 8. Third-Party Risk Management & Due Diligence

Per NCUA Letters 07-CU-13 and 01-CU-20, and the FFIEC Outsourcing Technology Services booklet, complete and file the following:

### Due Diligence Checklist

- [ ] Obtain and review SOC 2 Type II report ([trust.cursor.com](https://trust.cursor.com))
- [ ] Obtain penetration test executive summary ([trust.cursor.com](https://trust.cursor.com))
- [ ] Review [Security page](https://www.cursor.com/security) and data flow architecture
- [ ] Review [Privacy Policy](https://cursor.com/privacy) and [Terms of Service](https://cursor.com/terms-of-service)
- [ ] Review [Data Use policy](https://cursor.com/data-use)
- [ ] Identify and assess all subprocessors (listed on security page)
- [ ] Confirm zero data retention agreements with all LLM providers
- [ ] Confirm data residency (US-based for Privacy Mode)
- [ ] Evaluate business continuity and disaster recovery capabilities
- [ ] Assess financial viability (Series D: $2.3B raised; $1B+ ARR)
- [ ] Confirm no China-based infrastructure or subprocessors
- [ ] Evaluate cyber liability and E&O insurance coverage

### Contractual Requirements

Your enterprise agreement should include at minimum:

- **Right to audit** or obtain annual SOC 2 reports and pen test results
- **Breach notification timeline** (recommend 24–72 hours)
- **Data handling addendum** specifying zero data retention commitments
- **Clearly allocated responsibilities** for security incidents
- **Termination provisions** with data deletion confirmation
- **Subprocessor change notification** requirement
- **SLA** for uptime and support response
- **Visibility into vendor model and data practices** (per Treasury FS AI RMF)
- **Update and change controls** with advance notice
- **Incident response coordination** procedures

### Ongoing Monitoring

| Cadence | Activity |
|---|---|
| **Annual** | Review SOC 2 report and pen test summary; full risk reassessment |
| **Quarterly** | Review security page and changelog for material changes; report metrics to governance |
| **Continuous** | Monitor usage analytics dashboard for anomalous patterns |
| **Event-Driven** | Immediate review on any Cursor security advisory or vulnerability disclosure |

---

## 9. Secure SDLC Integration

AI-generated code does not get a free pass. All code produced by Cursor must go through your existing secure development lifecycle:

1. **Peer code review** — Reviewers should be aware when code is AI-assisted
2. **Automated SAST/DAST scanning** — In the CI/CD pipeline, same as human-written code
3. **Unit and integration testing** — At your established coverage thresholds
4. **Bugbot review** — Configure as an additional check, not a replacement for human review
5. **Bugbot policy and security scans** — Align with your secure coding standards

**Acceptable Use Policy (develop and distribute):**

- ✅ Permitted: code generation, refactoring, debugging, documentation, test writing
- ❌ Prohibited: processing member PII, cardholder data, or credentials through Cursor
- ❌ Prohibited: pasting passwords, API keys, or member data into chat/prompts
- ✅ Required: all AI-generated code undergoes same review/testing as human-written code
- ✅ Required: report any suspected data exposure through Cursor immediately

---

## 10. Phased Rollout Plan

### Phase 1: Foundation (Weeks 1–4)

- Complete vendor due diligence and execute the enterprise agreement
- Configure SSO, SCIM, Privacy Mode, model restrictions, MCP controls
- Deploy `.cursorignore` template to all repositories
- Publish the Cursor Acceptable Use Policy
- Configure network allowlisting
- Conduct risk assessment and document findings
- Brief your IT Steering Committee or governance body

### Phase 2: Pilot (Weeks 5–8)

- Deploy to 3–5 developers on non-PCI, non-member-data projects
- Monitor usage analytics for anomalous patterns
- Collect developer feedback on productivity and usability
- Validate CI/CD security controls work correctly with AI-generated code
- Verify `.cursorignore` enforcement in practice
- Conduct mid-pilot risk reassessment

### Phase 3: Controlled Expansion (Weeks 9–16)

- Expand to the full development team with training
- Enable Bugbot on all active repositories
- Establish reporting metrics (code review pass rates, security findings, productivity)
- Conduct training on secure AI tool use, prompt hygiene, and the AUP
- Begin preparing examiner-readiness documentation

### Phase 4: Steady State (Ongoing)

- Execute ongoing monitoring program
- Annual risk reassessment and vendor re-evaluation
- Update policies as NCUA, FFIEC, and Treasury guidance evolves
- Quarterly metrics reporting to governance
- Maintain examiner-ready documentation package
- Evaluate new Cursor features (on-prem if offered) for risk reduction

---

## 11. Examiner Readiness

NCUA examiners are increasingly asking about AI usage, policies, and risk assessments. Prepare this documentation package:

- [ ] This adoption plan (or your institutional version)
- [ ] Executed enterprise agreement
- [ ] SOC 2 Type II report and pen test summary
- [ ] Cursor Acceptable Use Policy
- [ ] Evidence of Privacy Mode enforcement and SSO/SCIM configuration
- [ ] Usage analytics exports demonstrating monitoring activity
- [ ] `.cursorignore` templates and evidence of enforcement
- [ ] Evidence that AI-generated code goes through the same SDLC as human code
- [ ] Ongoing monitoring log (quarterly and annual reviews)

**How to frame it for examiners:** Cursor is a developer productivity tool governed under your existing third-party risk management and information security programs, with AI-specific enhancements aligned to the NIST AI RMF and Treasury FS AI RMF. Privacy Mode means zero data retention. All code is human-reviewed. The tool augments — it does not replace — your secure SDLC.

---

## 12. Key Risks to Accept (Eyes Wide Open)

These are residual risks that your governance body should formally acknowledge:

| Risk | Why It Matters | Mitigation |
|---|---|---|
| **No self-hosted option** | All source code transits Cursor's AWS infrastructure, even in Privacy Mode. You are trusting their architecture. | Privacy Mode; contractual protections; `.cursorignore`; limit to non-restricted data |
| **No extension signature verification** | Marketplace extensions aren't signature-verified, unlike VS Code | Approved extension list; Workspace Trust enabled; endpoint management |
| **Embedding reversal (theoretical)** | Codebase embeddings in Turbopuffer could theoretically be partially reconstructed | Disable indexing for highly sensitive repos |
| **Cursor's own honest disclosure** | Their security page states: *"If you're working in a highly sensitive environment, you should be careful when using Cursor (or any other AI tool)."* | Risk-based scoping; don't use for Restricted data |
| **Evolving examiner expectations** | NCUA model risk management guidance is outdated; expectations will shift | Monitor for updated guidance; align with FS AI RMF proactively |

---

## 13. Quick-Reference Checklist

```
BEFORE DEPLOYMENT
├── [ ] Vendor due diligence complete (SOC 2, pen test, security review)
├── [ ] Enterprise agreement executed (audit rights, breach notification, ZDR)
├── [ ] Privacy Mode enforced organization-wide
├── [ ] SAML SSO configured with MFA at the IdP
├── [ ] SCIM provisioning enabled
├── [ ] Model access restricted to approved providers
├── [ ] MCP controls locked down
├── [ ] Network allowlist configured
├── [ ] .cursorignore template deployed to all repos
├── [ ] Workspace Trust enabled
├── [ ] Approved extension list published
├── [ ] Acceptable Use Policy distributed
├── [ ] Risk assessment documented and accepted
└── [ ] Governance body briefed and signed off

DURING PILOT
├── [ ] Monitoring usage analytics dashboard
├── [ ] Validating CI/CD security controls with AI-generated code
├── [ ] Collecting developer feedback
├── [ ] Verifying .cursorignore enforcement
└── [ ] Mid-pilot risk reassessment

STEADY STATE
├── [ ] Annual SOC 2 and pen test review
├── [ ] Quarterly security page and changelog review
├── [ ] Quarterly metrics reporting to governance
├── [ ] Continuous usage monitoring
├── [ ] Examiner-readiness documentation maintained
└── [ ] Policies updated as guidance evolves
```

---

## Resources & References

### NCUA
- [NCUA AI Resource Center](https://ncua.gov/regulation-supervision/regulatory-compliance-resources/artificial-intelligence-ai)
- [NCUA AI Compliance Plan](https://ncua.gov/ai/ncua-artificial-intelligence-compliance-plan)
- NCUA Letter 07-CU-13 — Evaluating Third Party Relationships
- NCUA Letter 01-CU-20 — Due Diligence Over Third Party Service Providers

### FFIEC
- [FFIEC IT Examination Handbook](https://ithandbook.ffiec.gov/)

### NIST
- [NIST AI Risk Management Framework](https://www.nist.gov/artificial-intelligence/executive-order-safe-secure-and-trustworthy-artificial-intelligence)
- NIST AI Cybersecurity Risk Profile (Preliminary Draft, December 2025)

### Treasury
- [Treasury FS AI RMF Press Release (Feb 2026)](https://home.treasury.gov/news/press-releases/sb0401)
- [Treasury Report: Artificial Intelligence in Financial Services](https://home.treasury.gov/system/files/136/Artificial-Intelligence-in-Financial-Services.pdf)

### GAO
- [GAO-25-107197: AI Use and Oversight in Financial Services (May 2025)](https://www.gao.gov/assets/gao-25-107197.pdf)

### Cursor
- [Cursor Enterprise](https://cursor.com/enterprise)
- [Cursor Security](https://www.cursor.com/security)
- [Cursor Trust Center](https://trust.cursor.com)
- [Cursor Privacy Policy](https://cursor.com/privacy)
- [Cursor Data Use](https://cursor.com/data-use)
- [Cursor Terms of Service](https://cursor.com/terms-of-service)

---

## Contributing

This guide is maintained as a community resource. If you have suggestions, corrections, or want to share how your credit union approached Cursor adoption, please open an issue or submit a pull request.

**Disclaimer:** This guide is provided for informational purposes only and does not constitute legal, regulatory, or compliance advice. Each credit union should consult with its own compliance, legal, and information security teams to determine the appropriate approach for its specific circumstances. Regulatory guidance is evolving rapidly — always validate against the latest publications from your regulators.

---

*Last updated: March 2026*
