---
name: security-hardening-audit
description: Perform comprehensive security audits, vulnerability detection, and defensive code hardening. Use when reviewing code for security flaws, auditing authentication/authorization, checking API endpoints, reviewing database rules (RLS), scanning for exposed secrets or injection risks, or hardening mobile/web applications before deployment.
---

# Security Hardening and Vulnerability Audit

Perform a thorough security review and provide actionable, code-level fixes to protect applications against exploitation, unauthorized data access, and supply-chain vulnerabilities.

## When to Use

- Auditing new or existing code for security vulnerabilities (OWASP Top 10 / OWASP Mobile Top 10).
- Reviewing authentication flows, session management, JWTs, or OAuth implementations.
- Checking database access controls, SQL queries, and Row-Level Security (RLS) policies.
- Validating API endpoints, webhooks, payment flows, and input sanitization.
- Scanning repositories for hardcoded secrets, insecure dependencies, or misconfigurations before production release.

## Audit Workflow

### 1. Attack Surface & Secret Exposure Scan
- Inspect the codebase and configuration files for hardcoded API keys, tokens, private keys, or credentials.
- Verify that `.env` files and local secret stores are properly excluded in `.gitignore`.
- Check client-side bundles (mobile or web) to ensure sensitive service-role keys or private secrets are never exposed on the client.

### 2. Authentication, Session & Access Control (IDOR / BOLA)
- Verify that every protected endpoint and server action validates both **authentication** (who the user is) and **authorization** (whether the user owns or has permission to access the specific resource).
- Check for Insecure Direct Object References (IDOR) where user-supplied IDs are queried without ownership filtering.
- If using Row-Level Security (e.g., Supabase/PostgreSQL), verify that RLS is enabled on all public tables and policies strictly enforce `auth.uid()` ownership checks rather than permissive `true` rules.

### 3. Input Validation & Injection Defense
- Ensure all external inputs (query params, request bodies, headers, webhooks, deep links) are validated with strict schemas (e.g., Zod) before processing.
- Check for SQL injection (raw string concatenation in queries), NoSQL injection, Command Injection (unsanitized shell execution), and Cross-Site Scripting (XSS via unsafe HTML rendering).
- Verify file upload endpoints validate MIME types, file extensions, size limits, and sanitize filenames against path traversal (`../`).

### 4. API, Webhook & Business Logic Security
- Verify cryptographic signature validation on all incoming webhooks (e.g., Stripe, payment gateways, auth providers) using raw request bodies before trusting payloads.
- Check rate limiting and brute-force protections on sensitive routes (login, OTP, password reset, checkout).
- Ensure sensitive operations use atomic transactions or idempotency keys to prevent race conditions and replay attacks.

### 5. Transport, Headers & Dependency Hygiene
- Enforce HTTPS/TLS, strict CORS origins (avoid wildcard `*` on authenticated endpoints), and secure cookie flags (`HttpOnly`, `Secure`, `SameSite`).
- Check dependencies for known critical CVEs and outdated packages with unpatched vulnerabilities.

## Output Format

Present findings ordered by severity (**Critical**, **High**, **Medium**, **Low**). For each issue, provide:

1. **Vulnerability & Location:** Exact file path and line numbers.
2. **Exploit Scenario:** Concise explanation of how an attacker could exploit the flaw.
3. **Remediation Code:** Drop-in, secure replacement code following the project existing stack and conventions.

If no issues are found in a category, briefly confirm what was verified so the audit trail is clear.
