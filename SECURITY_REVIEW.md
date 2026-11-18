# Security Review — Nishant-portfolio

Code-grounded review against the standard 20-point pen-test checklist. This is Nishant's
personal portfolio: a fully static, hand-written single `index.html` (GSAP/ScrollTrigger/Lenis
for animation, self-hosted fonts) deployed on Netlify with no backend, no accounts, and no form
submission — contact is a plain `mailto:` link. The only runtime network call is a read-only
fetch of the public GitHub API to list repos, with a hardcoded fallback list.

| # | Check | Result |
|---|-------|--------|
| 1 | HTTP/2 Rapid Reset (CVE-2023-44487) | N/A — static site, no custom server |
| 2 | bcrypt 72-byte truncation | N/A — no auth/passwords |
| 3 | SAML signature bypass (CVE-2024-45409) | N/A — no SAML/SSO |
| 4 | nOAuth | N/A — no OAuth |
| 5 | GraphQL alias batching | N/A — no GraphQL |
| 6 | TOTP replay (RFC 6238 §5.2) | N/A — no TOTP/2FA |
| 7 | ReDoS | No — no user-supplied regexes anywhere in this repo |
| 8 | JWT algorithm confusion | N/A — no JWTs |
| 9 | HTTP request smuggling | N/A — no server |
| 10 | Web cache poisoning | No — `_headers` sets fixed, non-request-header-driven cache rules for static assets only |
| 11 | Spoofed X-Forwarded-For | N/A — no backend reads request headers |
| 12 | Postgres views bypassing RLS | N/A — no database |
| 13 | `Math.random()` for security tokens | No — `Math.random()` is used only for the decorative hero point-cloud animation (`index.html:1087-1094`); the salary-visualization dot scatter uses its own seeded linear-congruential PRNG (`index.html:1220`, `seed=7`), also purely decorative, not security-relevant |
| 14 | Timing attacks on secret comparison | N/A — no secrets |
| 15 | Padding oracle | N/A — no encryption |
| 16 | Server-side prototype pollution | N/A — no server |
| 17 | Server-side template injection | N/A — no server-side templating |
| 18 | XXE | N/A — no XML parsing |
| 19 | Cross-site WebSocket hijacking | N/A — no WebSocket use |
| 20 | Unicode username normalization | N/A — no usernames/accounts |

## Findings outside the 20-point list

None. The one place external (if low-risk) data reaches `innerHTML` is handled correctly:
`renderRepos()` (`index.html:1038`) builds each repo card from the public GitHub API response
and runs every field — `r.url` (used in `href`), `r.name`, and the description — through `esc()`
(`index.html:935`, full `&<>"'` escaping) before interpolating it, including the one field an
attacker could theoretically influence if they ever compromised the account (a repo
name/description), so even that stays safe. The decorative SVG dots in `vizSalary()`
(`index.html:1223`) are built entirely from the function's own seeded PRNG output, not from any
external or user data. `_headers` already sets `X-Content-Type-Options`, `X-Frame-Options`,
`Referrer-Policy`, and a restrictive `Permissions-Policy`. No contact form, API endpoint, or
credential of any kind exists in the repo.

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>
Claude-Session: https://claude.ai/code/session_01RCiZNqYmetGt5SuieaZQcC
