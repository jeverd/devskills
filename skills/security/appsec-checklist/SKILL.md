---
name: appsec-checklist
description: Application security checklist — catch common vulns before they ship
category: security
tags: [security, checklist, owasp]
---

# Application security checklist

A practical checklist for catching security issues before they reach production.

## When to use

- Before deploying a new service
- During code review for security-sensitive changes
- As part of your regular PR review process

## Steps

### Input handling

- [ ] All user input is validated (type, length, format, range)
- [ ] SQL uses parameterized queries — no string interpolation
- [ ] HTML output is escaped (XSS prevention)
- [ ] File uploads are validated (type, size, name) and stored outside webroot
- [ ] API inputs have explicit schemas (not loose JSON parsing)

### Authentication & authorization

- [ ] Passwords are hashed with bcrypt/scrypt/argon2 — never MD5/SHA
- [ ] Session tokens are random, not predictable
- [ ] Auth decisions happen server-side — never trust client claims
- [ ] RBAC or ABAC for authorization, not hardcoded role checks
- [ ] Rate limiting on auth endpoints (login, password reset)

### Data protection

- [ ] Sensitive data encrypted at rest (PII, credentials, tokens)
- [ ] TLS for all data in transit (no HTTP endpoints)
- [ ] No secrets in code, configs, or git history
- [ ] Logs don't contain PII, tokens, or passwords
- [ ] Database backups are encrypted

### API security

- [ ] Input rate limiting per endpoint
- [ ] CORS configured explicitly — no `Access-Control-Allow-Origin: *`
- [ ] No stack traces or internal details in error responses
- [ ] API versioning to allow safe deprecation
- [ ] Pagination on list endpoints to prevent enumeration

### Infrastructure

- [ ] Dependencies scanned for known vulnerabilities (`npm audit`, `pip audit`)
- [ ] Container images are minimal — no shell access in production
- [ ] Secrets managed via vault/env, not environment variables in CI
- [ ] Security headers set (CSP, HSTS, X-Frame-Options)
- [ ] HTTPS redirect enforced

## Pitfalls

- **Rolling your own crypto** — use established libraries. Never implement encryption yourself.
- **Trusting client-side validation** — it's for UX only. Server must re-validate everything.
- **Forforcing complexity rules** — "8 chars + upper + number + symbol" is worse than passphrases. Encourage length, not arbitrary character classes.
- **Security through obscurity** — hiding endpoints doesn't secure them. Auth every route.
- **One-time security review** — security is continuous, not a phase.

## Verification

- ✅ Every checkbox above has been considered (checked or explicitly waived with a reason)
- ✅ Automated dependency scanning is in CI
- ✅ You've done a threat model for your top 3 risk areas
- ✅ Error responses don't leak internal details