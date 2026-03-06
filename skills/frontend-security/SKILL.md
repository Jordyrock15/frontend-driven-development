---
name: frontend-security
description: Use when writing any frontend code that handles user input, authentication, data display, or external data - enforces XSS prevention, secure auth patterns, CSP, and input sanitization
---

## Core Principle

Frontend security is defence in depth. Never rely solely on server-side validation. The frontend is a trust boundary.

## XSS Prevention

- NEVER use `dangerouslySetInnerHTML` without sanitization (DOMPurify or similar).
- If you must render HTML, sanitize it. Default answer is: don't render raw HTML.
- Use framework auto-escaping (React JSX escapes by default — don't bypass it).
- Sanitize URL schemes — reject `javascript:` in href attributes.
- CSP headers: set `Content-Security-Policy` to restrict inline scripts and `unsafe-eval`.

## Authentication & Session Security

- Store tokens in httpOnly cookies, NOT localStorage/sessionStorage (XSS can read storage).
- Set cookie flags: `Secure`, `HttpOnly`, `SameSite=Strict` (or Lax).
- Implement CSRF tokens for state-changing requests.
- Clear auth state on logout (tokens, cached user data, cookies).
- Handle token expiry gracefully — redirect to login, don't show broken UI.

## Input Handling

- Validate and sanitize all user input on the client (defence in depth).
- Use Zod or similar for runtime validation of form data.
- Escape user content before rendering in any context.
- File uploads: validate type, size, and name on client before sending.
- Never trust URL parameters — validate/sanitize before use.

## Secrets & Environment

- NEVER put API keys, secrets, or credentials in client-side code.
- Use environment variables with `NEXT_PUBLIC_` prefix only for truly public values.
- Audit bundle output — secrets in imports will end up in the browser.
- `.env.local` for development, server-side env vars for secrets.

## Dependencies

- Audit dependencies regularly (`npm audit`, `pnpm audit`).
- Avoid packages with known vulnerabilities.
- Pin dependency versions for reproducible builds.
- Prefer well-maintained packages with active security response.

## Anti-Patterns

| Anti-pattern | Risk |
|---|---|
| `dangerouslySetInnerHTML` without sanitization | XSS — attacker executes arbitrary JS |
| Auth tokens in localStorage | XSS can steal tokens, full account takeover |
| `javascript:` in href without validation | XSS via URL injection |
| API keys in frontend bundle | Credential theft, quota abuse |
| Disabled CORS / `Access-Control-Allow-Origin: *` | Cross-origin data theft |
| No CSRF protection on mutations | Unauthorized state changes |
| Eval or new Function with user input | Remote code execution |
