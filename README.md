# 🛡️ OWASP Multi-Repo Security Agent

> A GPT-4o powered security agent that hunts vulnerabilities across your **entire microservice estate** — not just one repo at a time.

---

## Why This Exists

Every SAST tool scans one repo. But your attack surface isn't one repo — it's 20 microservices, shared libraries, copy-pasted patterns, and the same hardcoded secret in six places.

This agent sees all of it at once.

---

## What It Does

- 🔍 **Discovers & scans every repo** in your GitHub org automatically
- 🤖 **GPT-4o reasons about each finding** — reads the actual code context to filter false positives
- ⚡ **Cross-service correlation** — finds the same vulnerability pattern across multiple services and flags it as a systemic issue
- 🛡 **CVE dependency scanning** — checks `requirements.txt` / `pom.xml` against OSV.dev (free, no key needed)
- 🧠 **Diff mode** — remembers previous scans, only alerts on *new* findings
- 📊 **Rich HTML dashboard** — heatmap, confidence scores, filterable findings table, LLM-written executive summary

### OWASP Coverage

| Framework | Examples |
|---|---|
| **Web Top 10 (2021)** | SQLi, Command Injection, Hardcoded Secrets, JWT bypass, SSRF, Pickle/YAML RCE |
| **API Top 10 (2023)** | BOLA/IDOR, Mass Assignment, No Pagination, Stale Versions |
| **LLM Top 10 (2025)** | Prompt Injection, Unsafe LLM Output, Excessive Agency, Supply Chain |

---

## Versions

| File | Description |
|---|---|
| `owasp_agent.py` | v1 — fast multi-repo scanner, cross-service correlation, no LLM needed |
| `owasp_agent_v2.py` | v2 — full GPT-4o agent with false positive filtering, CVE scanning, diff mode |

---

## Quickstart

```bash
pip install openai requests
```

**Scan your entire GitHub org:**
```bash
python owasp_agent_v2.py \
  --org your-org \
  --github-token ghp_xxx \
  --openai-key sk-xxx
```

**Scan specific microservices:**
```bash
python owasp_agent_v2.py \
  --org your-org \
  --repos auth-svc,payment-svc,ai-api \
  --github-token ghp_xxx \
  --openai-key sk-xxx
```

**Scan locally cloned repos (no GitHub token needed):**
```bash
python owasp_agent_v2.py \
  --local /path/to/your/services \
  --org your-org \
  --openai-key sk-xxx
```

**Use environment variables:**
```bash
export GITHUB_TOKEN=ghp_xxx
export OPENAI_API_KEY=sk-xxx
python owasp_agent_v2.py --org your-org
```

---

## Output

Open `owasp_v2_dashboard.html` in your browser. You get:

- Executive summary written by GPT-4o
- Cross-service systemic pattern alerts
- Repository × OWASP heatmap
- Per-repo risk score (A–F) with confidence-weighted findings
- CVE vulnerable dependency table
- Filterable findings table with "New findings only" toggle

---

## Supported Languages

Python · Java · Kotlin

---

## How It's Different From a SAST Scanner

| | SAST Scanner | This Agent |
|---|---|---|
| Scans multiple repos | ❌ One at a time | ✅ Entire org |
| Cross-service patterns | ❌ | ✅ |
| Same secret in N repos | ❌ | ✅ |
| OWASP LLM Top 10 | ❌ | ✅ |
| False positive reasoning | ❌ | ✅ GPT-4o reads code context |
| CVE dependency check | Sometimes | ✅ OSV.dev |
| Diff / new-findings-only | ❌ | ✅ |
