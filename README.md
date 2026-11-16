<div align="center">

# 🔗 LinkGuard — Open-Source URL Security Analysis

### Detect suspicious links. Understand the risks. Browse more safely.

LinkGuard combines URL heuristics, domain intelligence and threat-intel integrations into
**transparent, explainable risk reports** — before you click.

[![License: MIT](https://img.shields.io/badge/license-MIT-34d399?style=flat-square)](LICENSE)
[![Node.js ≥ 22](https://img.shields.io/badge/Node.js-%E2%89%A5%2022-38bdf8?style=flat-square)](https://nodejs.org)
[![TypeScript strict](https://img.shields.io/badge/TypeScript-strict-818cf8?style=flat-square)](#-tech-stack)
[![PRs welcome](https://img.shields.io/badge/PRs-welcome-fbbf24?style=flat-square)](CONTRIBUTING.md)
[![Status](https://img.shields.io/badge/status-early%20development-c084fc?style=flat-square)](ROADMAP.md)

**🌐 Live preview (landing page + interactive demo):**
`https://<piuskay-hue>.github.io/linkguard/`  deployed from this repo via GitHub Pages.

</div>

---

## ⚠️ The problem

Every day, millions of links are **designed to deceive**. Suspicious URLs arrive through email,
messaging apps, social networks, ads and websites — and most people have no reliable way to tell
them apart from legitimate ones.

| | Threat | Why it matters |
|---|---|---|
| 🏠 | **Malicious links are everywhere** | Phishing and malware distribution rely on one simple trick: a URL that looks trustworthy. They spread through inboxes, chats, social feeds and sponsored results. |
| 🎭 | **Impersonation is easy** | Homograph domains, raw IP hosts, deep subdomains and punycode can make a hostile address look exactly like a service you trust — or hide it completely. |
| 🧩 | **Existing tools fall short** | Many checkers give a bare verdict with no evidence, require paid access, or offer no developer-friendly integration. LinkGuard is built to be transparent, extensible and open. |

---

## ✨ What LinkGuard will do

A modular platform in active early development — each capability below is specified in the project
documentation and on the public [roadmap](ROADMAP.md).

### 🔍 URL analysis `scanner`
- Parsing & normalization that preserve the intended destination
- Suspicious pattern & IP-based URL detection
- Misleading subdomain identification
- Punycode / internationalized domain analysis
- Suspicious URL-encoding detection

### 🛡️ Threat intelligence `threat-intel`
- Google Safe Browsing integration
- Optional VirusTotal integration
- Domain reputation checks
- Known malicious URL identification
- Provider response caching

### 📊 Explainable assessment `scanner`
- Risk scoring with visible evidence, not a black box
- Phishing indicator analysis
- Controlled redirect inspection
- Domain & certificate info where available
- Detailed, structured security reports

### 🖥️ User platform `apps/web`
- Responsive web dashboard
- Fast URL scanning interface
- Authenticated scan history
- Shareable reports
- Visual risk breakdowns

### ⚙️ Developer platform `apps/api`
- Documented REST API with OpenAPI schemas
- Consistent JSON errors & request IDs
- Docker deployment
- Automated testing & CI/CD
- Built for extension & contribution

### 👁️ Transparency first `philosophy`
- Findings separated from confirmed intel matches
- Confidence reported alongside evidence
- Missing intel never treated as proof of safety
- Honest status communication

---

## 🧪 Live preview

The landing page (`index.html`, served at the repo Pages URL) includes an **interactive demo** that
runs a simplified set of the planned heuristics — entirely in your browser. Nothing is submitted
anywhere; the production engine adds threat-intel lookups on top.

Example of what the preview reports:

```text
URL: http://paypal.com.secure-login.verify-account.tk/signin
CRITICAL  Punycode (IDN) hostname          — labels that can visually imitate ASCII domains
HIGH      Sensitive keyword "paypal"       — frequently abused for credential-harvesting
MODERATE  Deep subdomain chain (6 levels)  — pushes the real destination out of view
MODERATE  High-abuse TLD ".tk"             — heavily abused in phishing campaigns
HIGH      Credentials embedded in URL      — the part before "@" is decoration

Risk score: 62/100 → HIGH RISK
```

---

## ⚙️ How it works

From submission to explainable verdict — every scan follows the same disciplined pipeline,
specified in `ARCHITECTURE.md`, with strict validation and network isolation at each stage.

| Stage | Step | Description |
|---|---|---|
| 01 | **Submit** | A URL arrives through the web dashboard or the REST API. |
| 02 | **Validate** | Strict scheme allowlist and input validation reject malformed requests. |
| 03 | **Parse & normalize** | The scanner normalizes safely while preserving the original evidence. |
| 04 | **Local heuristics** | Detection rules inspect structure, hosts, encoding and punycode. |
| 05 | **Domain intel** | DNS and domain information is collected where supported. |
| 06 | **Threat intel** | Configured providers are queried; failures never crash the scan. |
| 07 | **Score & explain** | Findings are weighted into a transparent risk assessment. |
| 08 | **Report** | A structured report is stored under retention policy and returned. |

---

## 🎯 Risk model

Scoring that **shows its work** — weighted, deduplicated, and always traceable back to the
evidence behind it.

| Score | Risk level |
|:---:|:---|
| 0 – 24 | 🟢 **Low** |
| 25 – 49 | 🟡 **Moderate** |
| 50 – 74 | 🟠 **High** |
| 75 – 100 | 🔴 **Critical** |

**Anatomy of a finding** — every score is the sum of individually documented findings, each
carrying its full context:

- **Detection ID** — stable identifier for rules & docs
- **Description** — what was detected, in plain language
- **Severity & weight** — contribution to the total score
- **Evidence** — the exact part of the URL that triggered it

---

## 🏗️ Architecture

```text
linkguard/
├── apps/
│   ├── web/              # React dashboard
│   └── api/              # Fastify REST API
├── packages/
│   ├── scanner/          # analysis engine
│   ├── threat-intel/     # providers
│   ├── shared/           # types & schemas
│   └── config/           # shared configs
├── docs/                 # 9 technical documents
├── tests/                # three test suites
└── .github/              # templates & workflows
```

**Infrastructure:** PostgreSQL 17 · Redis 7 · BullMQ · Prisma · Docker Compose · GitHub Actions ·
pnpm + Turborepo

---

## 🔐 Security by design

A URL scanner is a **high-risk surface** — treated like one. Fetching untrusted destinations is
dangerous by definition, so LinkGuard's threat model (`SECURITY_DESIGN.md`) demands verifiable
controls before any remote fetching is enabled.

| Control | What it does |
|---|---|
| **SSRF prevention** | Strict protocol allowlisting and resolution validation on every request. |
| **DNS rebinding defense** | All resolved addresses re-checked at connection time, not just first lookup. |
| **Private IP blocking** | Loopback, link-local, private and reserved ranges are always denied. |
| **Redirect revalidation** | Every hop is re-resolved and independently re-checked, with hard limits. |
| **No script execution** | Submitted websites are never rendered or executed by the scanner. |
| **Rate limiting** | Public endpoints are protected from abuse with configurable quotas. |
| **Credential redaction** | Sensitive query parameters are excluded from persistent storage by default. |
| **Hard limits** | Timeouts, response-size caps and isolated workers for all fetch jobs. |

> ✅ **Verified before launch:** security testing must cover SSRF, DNS rebinding, redirect chains,
> private-IP access, malformed input and resource exhaustion — controls are verified before
> arbitrary remote URL fetching is enabled.

---

## 🧰 Tech stack

| Layer | Tools |
|---|---|
| **Frontend** | React · Vite · TypeScript · Tailwind CSS |
| **Backend** | Node.js 22 · Fastify · Zod |
| **Data** | PostgreSQL · Prisma · Redis · BullMQ |
| **Quality** | Vitest · OpenAPI · Turborepo · pnpm |
| **Ops** | Docker · Compose · GitHub Actions |

Chosen for type safety, performance and a contributor-friendly developer experience.

---

## 🔌 Developer-first REST API

Submit scans, poll results and integrate LinkGuard into your own tooling — with schema-based
validation, consistent errors and request IDs throughout.

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/v1/scans` | Submit a URL for analysis |
| `GET` | `/api/v1/scans/:id` | Retrieve a scan report |
| `GET` | `/api/v1/scans` | Authenticated scan history |
| `GET` | `/api/v1/providers` | Provider availability |
| `GET` | `/api/v1/health` | Service health check |

```http
POST /api/v1/scans HTTP/1.1
Content-Type: application/json

{ "url": "https://example.com/login" }
```

```json
{
  "id": "scan_8f14e45f",
  "status": "queued",
  "riskScore": null,
  "report": null,
  "links": { "self": "/api/v1/scans/scan_8f14e45f" }
}
```

---

## 🗺️ Roadmap

From foundation to first stable release — tracked publicly in
[ROADMAP.md](ROADMAP.md). Items are only marked complete after implementation and verification.

- [x] **01 — Foundation** *(in progress)*: repository structure · product specification · documentation foundation
- [ ] **02 — Scanner engine**: parsing & normalization · hostname/IP/punycode analysis · suspicious pattern rules · initial scoring + tests
- [ ] **03 — Backend API**: Fastify application · validation & orchestration · rate limiting · OpenAPI documentation
- [ ] **04 — Persistence & jobs**: PostgreSQL + Prisma · scan persistence · Redis + BullMQ workers · retention policies
- [ ] **05 — Threat intelligence**: provider interface · Google Safe Browsing · VirusTotal · caching & failure handling
- [ ] **06 — Frontend**: React application · scanner interface · results dashboard · scan history
- [ ] **07 — Security hardening**: SSRF & DNS rebinding controls · redirect validation · network isolation · security testing
- [ ] **08 — Release**: Docker deployment · CI/CD pipelines · integration testing · first public release

---

## 🤝 Contributing

Help make URL security **transparent** — LinkGuard is MIT-licensed and built for contributors.
Bug reports · documentation · detection rules · code — every contribution counts.
See [CONTRIBUTING.md](CONTRIBUTING.md) to get started, and ⭐ **star the repository** to follow
development.

---

## 📄 License

[MIT](LICENSE) © LinkGuard Contributors — *"Built with transparency, not fear."*

---

> **Disclaimer:** LinkGuard provides security assessments based on available indicators and
> threat-intelligence sources. A low-risk result does not guarantee that a URL is safe — coverage
> varies and new malicious sites may not yet be identified. Always exercise caution with
> unfamiliar links. This repository describes a project in early development; capabilities marked
> "planned" are not yet implemented.
