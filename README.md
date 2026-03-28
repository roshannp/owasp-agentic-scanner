# Agentic-scanner

A GPT-4o powered security agent that hunts vulnerabilities across your entire microservices org — not just one repo.

Most SAST tools scan one repo in isolation. This agent scans your whole environment, correlates findings across services, digs through git history, checks CVE databases, and writes its own executive summary. It thinks, adapts, and remembers between scans.

---

## What Makes This Different

| Feature | Traditional SAST | This Agent |
|---|---|---|
| Scope | Single repo | Entire org / all microservices |
| False positives | Many | GPT-4o reads code context, scores confidence 1-10 |
| Cross-service patterns | No | Shared secrets, CORS spread, systemic issues |
| CVE scanning | No | OSV.dev (PyPI + Maven, free, no key needed) |
| Git history | No | Finds secrets deleted years ago |
| Who introduced it | No | git blame per finding |
| Diff / memory mode | No | Only alerts on new findings between scans |
| Output | Raw findings | Executive summary + interactive HTML dashboard |

---

## OWASP Coverage

- **Web Top 10 (2021)** — Injection, Broken Access Control, Cryptographic Failures, SSRF, Security Misconfiguration, and more
- **API Top 10 (2023)** — BOLA, Mass Assignment, Broken Auth, Resource Consumption, Inventory Management
- **LLM / AI Top 10 (2025)** — Prompt Injection, Insecure Output Handling, Excessive Agency, Sensitive Data Exposure, Supply Chain

40+ rules across Python, Java, and Kotlin.

---

## Quickstart

```bash
pip install openai requests

# Scan a GitHub org
python owasp_agent_v2.py \
  --org your-org \
  --github-token ghp_xxx \
  --openai-key sk-xxx

# Scan local repos (no GitHub token needed)
python owasp_agent_v2.py \
  --local ./path/to/repos \
  --openai-key sk-xxx

# Use env vars to avoid passing keys on the command line
export GITHUB_TOKEN=ghp_xxx
export OPENAI_API_KEY=sk-xxx
python owasp_agent_v2.py --org your-org
```

---

## Output

```
owasp_v2_dashboard.html   — interactive dashboard, open in any browser
owasp_v2_report.json      — machine-readable findings
owasp_memory.json         — scan history for diff mode
```

The dashboard includes a GPT-4o executive summary, A-F risk grade per repo and org-wide, severity and category heatmap, a filterable findings table with confidence scores, a CVE tab with CVE IDs and CVSS scores, and a new-findings-only toggle for diff mode.

---

## How the Agent Works

This is a real ReAct agent (Reason → Act → Observe → Repeat) built on GPT-4o tool calling — not a fixed script.

```
Step 1  LIST      — discover all repos in the org
Step 2  SCAN      — clone + run 40+ OWASP regex rules across every file
Step 3  REVIEW    — read +/-15 lines of context, filter false positives, score confidence
Step 4  CVE       — check requirements.txt / pom.xml against OSV.dev
Step 5  HISTORY   — git log pickaxe search for secrets across full commit history
Step 6  BLAME     — git blame to identify who introduced each vulnerability
Step 7  CORRELATE — find systemic patterns across all repos
Step 8  REPORT    — write executive summary, generate dashboard
```

GPT-4o decides the scan order, adapts based on what it finds (if it spots AI code it digs deeper on LLM Top 10), and reasons about each finding before confirming it.

---

## CLI Reference

```
--org              GitHub org name (auto-detected from folder name with --local)
--github-token     GitHub PAT with repo scope (or set GITHUB_TOKEN env var)
--openai-key       OpenAI API key (or set OPENAI_API_KEY env var)
--local            Path to a local folder containing repo directories
--repos            Comma-separated list of specific repos to scan
--exclude          Comma-separated list of repos to skip
--output           HTML output path (default: owasp_v2_dashboard.html)
--json-out         JSON output path (default: owasp_v2_report.json)
--memory-file      Scan memory path (default: owasp_memory.json)
--max-repos        Max repos to scan (default: 50)
--max-steps        Max agent iterations (default: 80)
```

---

## Test Environment

A setup script is included that creates 5 intentionally vulnerable microservice repos to validate the agent end-to-end.

```bash
chmod +x setup_vulnerable_repos.sh
./setup_vulnerable_repos.sh

python owasp_agent_v2.py --local ./vuln-org --openai-key sk-xxx
```

The environment includes hardcoded AWS and OpenAI keys committed then deleted, a JWT secret shared across all 5 services, SQL injection, SSRF, pickle deserialization, `eval(llm_response)`, prompt injection, CORS wildcards, and vulnerable dependencies with real CVEs — all with full git history to exercise commit scanning and git blame.

---

## Requirements

- Python 3.8+
- `pip install openai requests`
- OpenAI API key (GPT-4o)
- GitHub PAT with `repo` scope (GitHub org mode only)

CVE data is sourced from [OSV.dev](https://osv.dev) — free, no registration required.

---

## Files

| File | Description |
|---|---|
| `owasp_agent_v2.py` | Main agent — GPT-4o powered, recommended |
| `owasp_agent.py` | v1 — deterministic multi-repo scanner, no LLM required |
| `setup_vulnerable_repos.sh` | Creates a test environment with 5 vulnerable microservices |
