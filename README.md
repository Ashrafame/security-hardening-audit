# Security Hardening & Vulnerability Audit Skill

An open-standard Agent Skill (`SKILL.md`) for **Claude Code** and **OpenCode** that performs structured security audits, vulnerability detection, and code-level hardening across web, mobile, and backend repositories.

## What It Audits

- **Attack Surface & Secret Exposure:** Scans for hardcoded API keys, tokens, credentials, exposed service-role keys in client bundles, and improperly ignored `.env` files.
- **Authentication & Access Control (IDOR / BOLA):** Validates session checks, ownership filtering, and database Row-Level Security (RLS) policies.
- **Input Validation & Injection Defense:** Checks schema validation, SQL/NoSQL/Command injection, XSS risks, and file upload sanitization.
- **API, Webhook & Business Logic Security:** Verifies webhook cryptographic signatures, rate limiting, and atomic/idempotent operations.
- **Transport, Headers & Dependency Hygiene:** Reviews CORS policies, TLS/cookie security flags, and vulnerable dependencies.

## Installation

### Via Skills CLI
```bash
npx skills add ashrafame/security-hardening-audit
```

### Manual Installation (OpenCode & Claude Code)
```bash
mkdir -p ~/.claude/skills/security-hardening-audit ~/.config/opencode/skills
curl -fsSL https://raw.githubusercontent.com/ashrafame/security-hardening-audit/main/skills/security-hardening-audit/SKILL.md -o ~/.claude/skills/security-hardening-audit/SKILL.md
ln -sfn ~/.claude/skills/security-hardening-audit ~/.config/opencode/skills/security-hardening-audit
```

## License
MIT
