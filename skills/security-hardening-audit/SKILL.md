---
name: security-hardening-audit
description: Perform comprehensive security audits, vulnerability detection, and defensive code hardening across web, mobile, and multiplayer backends. Use when reviewing code for security flaws, auditing authentication/authorization, checking API endpoints, reviewing Supabase database rules (RLS) and Edge Functions, auditing React Native / Expo apps, checking multiplayer game state/anti-cheat logic, scanning for exposed secrets, or hardening applications before deployment.
---

# Security Hardening and Vulnerability Audit

Perform a thorough security review and provide actionable, code-level fixes to protect applications against exploitation, unauthorized data access, state tampering, and supply-chain vulnerabilities.

## When to Use

- Auditing new or existing code for security vulnerabilities (OWASP Top 10 / OWASP Mobile Top 10).
- Reviewing Supabase schemas, Row-Level Security (RLS) policies, RPC functions, and Edge Functions.
- Hardening React Native / Expo mobile applications (secret storage, deep links, client bundle exposure).
- Auditing real-time multiplayer game logic against client-side state tampering, race conditions, and replay attacks.
- Validating API endpoints, payment webhooks (e.g., Stripe), and input sanitization before production release.

## Audit Workflow

### 1. Attack Surface & Secret Exposure Scan
- Inspect the codebase and configuration files for hardcoded API keys, tokens, private keys, or credentials.
- Verify that `.env` files and local secret stores are properly excluded in `.gitignore`.
- Check client-side bundles (mobile or web) to ensure `SUPABASE_SERVICE_ROLE_KEY`, Stripe secret keys, or admin credentials are never bundled in client code (`EXPO_PUBLIC_*` must only hold safe public identifiers).

### 2. Authentication, Session & Access Control (IDOR / BOLA)
- Verify that every protected endpoint and server action validates both **authentication** (who the user is) and **authorization** (whether the user owns or has permission to access the specific resource).
- Check for Insecure Direct Object References (IDOR) where user-supplied IDs are queried without ownership filtering.

### 3. Supabase RLS, Database Functions & Edge Functions
- Verify that `ALTER TABLE ... ENABLE ROW LEVEL SECURITY` is active on **every** table in the `public` schema (and any exposed schemas).
- Flag overly permissive policies (`USING (true)` or `WITH CHECK (true)`i on `INSERT\, `UPDATE`, or `DELETE`, and ensure `SELECT` policies only expose intended columns/rows.
- Verify that `USING` and `WITH CHECK` clauses strictly bind mutations to `(select auth.uid()) = user_id` (or verified membership/role tables).
- Audit `SECURITY DEFINER` PostgreSQL functions/RPCs to ensure `SET search_path = public` (or `''`) is explicitly defined to prevent search-path hijacking, and verify caller authorization inside the function body.
- Check Supabase Storage bucket policies to prevent unauthorized file listing, overwrites, or public exposure of private assets.
- Verify Edge Functions authenticate incoming `Authorization` JWTs via `supabase.auth.getUser()` rather than trusting unverified client headers or payload `user_id` fields.

### 4. React Native & Expo Mobile Hardening
- Ensure sensitive tokens, session credentials, and private keys are stored in encrypted native storage (`expo-secure-store` / Keychain / Keystore) rather than plaintext `AsyncStorage` or MMKV without encryption.
- Validate all incoming deep links and universal links (`expo-linking` / `expo-router` params) with strict schemas before triggering navigation or state mutations.
- Check `app.json` / `app.config.ts` and native manifests (`AndroidManifest.xml` / `Info.plist`) for unintended exported activities/receivers, cleartext HTTP traffic (`usesCleartextTraffic` / `NSAllowsArbitraryLoads`), or debug flags left enabled in production builds.
- Verify sensitive screens or local state do not leak confidential data in app-switcher snapshots or console logs in production (`__DEV__` guards).

### 5. Multiplayer Game Logic & Real-Time Anti-Cheat
- **Server-Authoritative Validation:** Ensure critical game state transitions (deck shuffling, card dealing, move legality, turn order, score calculation, match resolution) are validated server-side or via trusted Edge Functions/RPCs rather than trusting client-emitted state updates.
- **Hidden Information Isolation:** Verify that secret state (e.g., opponents ' private hands, undealt deck order) is never broadcast over Supabase Realtime channels or returned in shared table rows readable by all players.
- **Turn & Sequence Integrity:** Check for race conditions and duplicate move submissions using atomic database locks/transactions, monotonic turn/action sequence numbers, or idempotency keys.
- **Presence & Disconnect Handling:** Verify that disconnects, reconnects, and timeout forfeits cannot be abused by a losing client to stall matches, duplicate rewards, or corrupt shared match state.

### 6. Input Validation, Webhooks & Transport Hygiene
- Ensure all external inputs (query params, request bodies, headers, webhooks) are validated with strict schemas (e.g., Zod) before processing.
- Check for SQL injection, Command Injection, XSS, and path traversal (`../`) on file uploads.
- Verify cryptographic signature validation on all incoming webhooks (e.g., Stripe) using raw request bodies before trusting payloads.
- Enforce HTTPS/TLS, strict CORS origins (avoid wildcard `*` on authenticated endpoints), rate limiting on sensitive routes, and dependency CVE checks.

## Output Format

Present findings ordered by severity (**Critical**, **High**, **Medium**, **Low**). For each issue, provide:

1. **Vulnerability & Location:** Exact file path and line numbers.
2. **Exploit Scenario:** Concise explanation of how an attacker or malicious player could exploit the flaw.
3. **Remediation Code:** Drop-in, secure replacement code following the project's existing stack and conventions.

If no issues are found in a category, briefly confirm what was verified so the audit trail is clear.
