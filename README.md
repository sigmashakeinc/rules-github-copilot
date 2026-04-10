# rules-github-copilot

GitHub Copilot governance rules — guards against CamoLeak secret exfiltration (CVSS 9.6), the Rules File Backdoor supply chain attack, credential suggestions from training data, and the high rate of security weaknesses in Copilot-generated code (29.5% of Python snippets).

**13 rules · 3 files**

![rules-github-copilot — AI agent governance demo](demo.cast)

> [▶ Watch interactive demo on SigmaShake Hub](https://hub.sigmashake.com/ruleset/rules-github-copilot)

## Install

```bash
ssg hub pull rules-github-copilot
```

Available on the [SigmaShake Hub](https://hub.sigmashake.com) — the open registry for AI agent governance rules. Compatible with Claude Code, Cursor, Windsurf, Aider, and any AI coding agent using the `ssg` hook protocol.

## Rules

### copilot_write_secret_exfil.rules — Secret exfiltration protection (5 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `no-copilot-zero-width-with-network` | DENY | error | CamoLeak (CVSS 9.6): blocks zero-width Unicode + HTTP combinations |
| `no-copilot-credential-suggestion` | DENY | error | Blocks hardcoded credential values from Copilot training data suggestions |
| `ask-copilot-base64-encoded-url` | ASK | warning | Prompts on base64-decoded URLs (CamoLeak encoding pattern) |
| `ask-copilot-dynamic-module-load` | ASK | warning | Prompts on dynamic import with concatenation (typosquat risk) |
| `log-copilot-dangerous-patterns` | LOG | info | Audits eval/innerHTML/dangerouslySetInnerHTML in Copilot suggestions |

### copilot_read_rules_backdoor.rules — Rules File Backdoor protection (4 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `no-copilot-hidden-chars-in-rules` | DENY | error | Blocks control characters in .copilot-rules files (backdoor indicator) |
| `ask-copilot-rules-file-modification` | ASK | warning | Prompts before writing any Copilot config/instructions file |
| `no-copilot-bidi-override-chars` | DENY | error | Blocks bidirectional text overrides and zero-width Unicode in code |
| `log-copilot-instruction-file-access` | LOG | info | Audit trail for all Copilot config file reads |

### copilot_exec_security.rules — Execution security (4 rules)

| Rule | Decision | Severity | Description |
|------|----------|----------|-------------|
| `no-copilot-token-in-command` | DENY | error | Blocks GitHub PAT, AWS key, Slack, and OpenAI tokens in shell commands |
| `ask-copilot-execute-generated-script` | ASK | warning | Prompts before executing Copilot-generated .py/.js/.sh scripts |
| `ask-copilot-package-install` | ASK | warning | Prompts on pip/npm install to catch typosquatted package suggestions |
| `log-copilot-pre-push` | LOG | info | Audit trail for git push after Copilot sessions |

## Why this matters

GitHub Copilot's access to your entire private codebase — and its suggestions trained on billions of lines of public code — creates two distinct threat surfaces:

**Incoming threats (code Copilot writes):**
- **CamoLeak** (CVSS 9.6, Legit Security, June 2025): A critical Copilot Chat vulnerability combined zero-width Unicode characters with GitHub's trusted infrastructure to silently exfiltrate secrets and source code from private repos. Attackers had full control over Copilot's responses.
- **Credential suggestions**: Research (CUHK) built an extraction algorithm showing Copilot suggests real credentials memorized from training data.
- **29.5% insecure code rate**: An ACM study found nearly 1 in 3 Copilot-generated Python snippets has security weaknesses — SQL injection, hardcoded secrets, eval on user input.

**Incoming threats (repo supply chain):**
- **Rules File Backdoor** (Pillar Security): Attackers embed invisible malicious instructions in `.copilot-rules` using hidden Unicode characters, silently poisoning Copilot suggestions for all repo contributors without being visible in code review.

## Compatible AI clients

- GitHub Copilot (primary target)
- Works alongside: `rules-security`, `rules-secrets`, `rules-npm`

## About

Part of the [SigmaShake Hub](https://hub.sigmashake.com) — open-source governance rules for AI coding agents.
Install the `ssg` CLI to enforce these rules: `npm install -g @sigmashake/ssg`
