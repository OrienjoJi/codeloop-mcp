# Security Policy

## Reporting a vulnerability

If you believe you've found a security vulnerability in CodeLoop — the MCP server, CLI, backend, website, or this marketing repository — please **do not open a public GitHub issue**.

Instead, email **[support@codeloop.tech](mailto:support@codeloop.tech)** with the subject line `[SECURITY]` and include:

1. A clear description of the vulnerability
2. Steps to reproduce
3. The affected version (`npx codeloop --version` and `npx codeloop-mcp-server --version`)
4. Your assessment of impact and severity
5. Whether you'd like public credit when the fix ships

We aim to:

- **Acknowledge** your report within 2 business days
- **Confirm or deny** the vulnerability within 7 business days
- **Ship a fix** within 30 days for high-severity issues, sooner for critical
- **Publish a public advisory** after the fix is widely deployed

## Scope

In scope:
- The CodeLoop MCP server (`codeloop-mcp-server` on npm)
- The CodeLoop CLI (`codeloop` on npm)
- The CodeLoop backend (`api.codeloop.tech`)
- The CodeLoop website (`codeloop.tech`)
- This marketing repository

Out of scope (please don't report these):
- Bugs in third-party tools we depend on (Playwright, ffmpeg, adb, etc.) — report those upstream
- Security issues in your own integration of CodeLoop (we'll happily help triage, but they aren't CodeLoop vulnerabilities)
- Self-XSS or social engineering targets
- Lack of rate limiting on demo / unauthenticated endpoints (we know — we have global rate limits)

## Safe-harbour

We will not pursue legal action against researchers who:

1. Make a good-faith effort to avoid privacy violations and service disruption
2. Only access the minimum data necessary to demonstrate the vulnerability
3. Give us a reasonable time to fix before publicly disclosing

Thank you for helping keep CodeLoop and our customers safe.

— The CodeLoop team
