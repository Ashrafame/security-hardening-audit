# Security Hardening & Vulnerability Audit Skill

An open-standard Agent Skill (`SKILL.md`) for **Claude Code** and **OpenCode** that performs structured security audits, vulnerability detection, and code-level hardening across web, mobile, and real-time multiplayer repositories.

## What It Audits

- **Attack Surface & Secret Exposure:** Scans for hardcoded API keys, tokens, exposed `SUPABASE_SERVICE_ROLE_KEY` or `EXPO_PUBLIC_*` leaks, and improperly ignored `.env` files.
- **Authentication & Access Control (IDOR / BOLA):** Validates session checks and ownership filtering across endpoints and server actions.
- **Supabase RLS, RPCs & Edge Functions:** Audits row-level security policies, `SECURITY DEFINER` search-path safety, Storage buckets, and JWT verification.
- +*React Native & Expo Hardening:** Checks `expo-secure-store` usage, deep link validation, native manifest permissions, and production debug flags.
- +*Multiplayer Game Logic & Anti-Cheat:** Enforces server-authoritative state transitions, hidden-hand isolation on Realtime channels, turn sequence integrity, and safe disconnect handling.
- **Input Validation, Webhooks & Transport Hygiene:** Verifies schema validation, Stripe webhook signatures, CORS, rate limiting, and dependency CVEs.

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
