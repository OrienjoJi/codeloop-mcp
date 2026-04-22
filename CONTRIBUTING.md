# Contributing

Thanks for your interest in CodeLoop! This repository is **marketing and documentation only** — the source code for the CodeLoop MCP server, CLI, backend, and website is hosted privately.

## What we welcome here

- **Documentation improvements** — typos, clarity, missing steps, broken links
- **Setup-guide additions** — covering frameworks, IDEs, or platforms not yet documented
- **FAQ entries** — questions you wish had been answered before you started
- **Screenshots and diagrams** — see [`docs/assets/SCREENSHOTS.md`](docs/assets/SCREENSHOTS.md)
- **Bug reports for the product itself** — open an issue with reproduction steps and we'll triage internally

## What we can't accept here

- Pull requests against the MCP server, CLI, backend, or website source code (they live in a private repo)
- Forks intended to redistribute CodeLoop as a different product (see [LICENSE](LICENSE))
- Reverse-engineering attempts

## How to file a useful bug report

1. Run `npx codeloop --version` and include the output.
2. Note your OS (`darwin` / `win32` / `linux`) and IDE (Cursor / Claude Code / other).
3. Note your project type (Flutter, Next.js, Xcode, etc.).
4. Include the contents of the relevant `artifacts/runs/<run_id>/meta.json` if available.
5. Include the agent's transcript or the error message you saw.

File issues at: [github.com/OrienjoJi/codeloop-mcp/issues](https://github.com/OrienjoJi/codeloop-mcp/issues)

## Commercial and partnership inquiries

Email [support@codeloop.tech](mailto:support@codeloop.tech).

## Code of Conduct

Be kind. Be technical. Be specific.
We follow the [Contributor Covenant v2.1](https://www.contributor-covenant.org/version/2/1/code_of_conduct/).

By contributing, you agree your contribution is licensed under the terms of [LICENSE](LICENSE).
