# Getting Started with CodeLoop

A 5-minute walk-through from zero to a closed verify-fix loop in your project.

> **TL;DR**
> ```bash
> npx codeloop init
> ```
> …then restart Cursor or Claude Code. That's it.

---

## Prerequisites

- **Node.js 20+** (22 recommended) — needed to run the MCP server and CLI
- **One of:** Cursor, Claude Code, or any MCP-compatible AI editor
- **A CodeLoop API key** — get one in 30 seconds at [codeloop.tech/signup](https://codeloop.tech/signup) (14-day free trial, no credit card)

CodeLoop also calls out to your existing project tooling when relevant — `flutter`, `npm`, `xcodebuild`, `gradlew`, `dotnet`, Playwright, Maestro, `adb`, `xcrun simctl` — but only the ones already installed for your stack. Nothing extra is forced on you.

---

## Path 1 — One command (recommended)

```bash
cd your-project
npx codeloop init
```

This single command will:

1. **Detect your project type** — Flutter, Next.js, Vue, Svelte, Xcode, .NET, Android Studio, or generic web.
2. **Ask for your API key** — paste it, or if you don't have one, choose "Sign up for a free trial" and the CLI will create your account inline.
3. **Configure Cursor** if it finds `.cursor/` — writes `.cursor/mcp.json` and copies the rule files into `.cursor/rules/`.
4. **Configure Claude Code** if it finds `.claude/` — writes `.claude/settings.local.json`, agent instructions, and slash commands.
5. **Create `.codeloop/config.json`** with your platform settings.
6. **Add itself to `.gitignore`** so your key never gets committed.
7. **Create the artifacts directory and spec/acceptance templates.**

Restart your IDE. The `codeloop_*` tools will appear in your agent's tool list and the rules will be loaded.

---

## Path 2 — Already have a key

```bash
npx codeloop init --key cl_live_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

Skips the interactive prompt entirely. Useful for scripting or for adding CodeLoop to many projects.

---

## Path 3 — Terminal-only (no browser)

```bash
npx codeloop signup       # Create account, get key, save to ~/.zshrc
source ~/.zshrc            # Load the new env var
npx codeloop init          # Set up the project (key already detected)
```

---

## Path 4 — Manual configuration

If you'd rather wire it up yourself:

1. Get your API key from [codeloop.tech/dashboard/keys](https://codeloop.tech/dashboard/keys).
2. Set it: `export CODELOOP_API_KEY=cl_live_...`
3. Add this to `.cursor/mcp.json` (Cursor) or `.claude/settings.local.json` (Claude Code):

   ```json
   {
     "mcpServers": {
       "codeloop": {
         "command": "npx",
         "args": ["-y", "codeloop-mcp-server"],
         "env": { "CODELOOP_API_KEY": "cl_live_xxx" }
       }
     }
   }
   ```

4. Copy the rule files from [codeloop.tech/docs/cursor](https://codeloop.tech/docs/cursor) or [codeloop.tech/docs/claude-code](https://codeloop.tech/docs/claude-code) into your project.

---

## Verify it's working

In Cursor or Claude Code, ask your agent:

> "List your CodeLoop tools."

You should see `codeloop_verify`, `codeloop_diagnose`, `codeloop_interact`, `codeloop_visual_review`, `codeloop_gate_check`, and ~15 others. If you don't, your MCP config isn't loaded — restart the IDE and try again.

Then ask:

> "Run codeloop_verify on this project and report what you find."

You'll see a structured report with build status, tests run/passed/failed/skipped, screenshots captured, and a recommendation for the agent's next move.

---

## What happens next

Once CodeLoop is wired up, the rules tell your agent:

- **After every code change**, call `codeloop_verify`.
- **If verify fails**, call `codeloop_diagnose` and fix the issues, **without asking the user**.
- **Before claiming a feature is done**, call `codeloop_gate_check`. If confidence is below 94%, **keep looping**.
- **Only when the gate returns `ready_for_review`**, hand control back to the human.

You become the reviewer of finished, verified work — not the QA team for half-finished code.

---

## Troubleshooting

| Symptom | Fix |
|---|---|
| `activation_required` from every tool | `CODELOOP_API_KEY` env var isn't set, or the key is invalid. Re-run `npx codeloop signup` or check [codeloop.tech/dashboard/keys](https://codeloop.tech/dashboard/keys). |
| Tools don't appear in Cursor | Restart Cursor after `npx codeloop init`. Check `.cursor/mcp.json` exists. |
| Tools don't appear in Claude Code | Restart Claude Code. Check `.claude/settings.local.json` exists. |
| `codeloop_verify` says "no tests detected" | Make sure your project has a recognizable test setup (`package.json` with test script, `pubspec.yaml`, `*.csproj`, `build.gradle`, etc.). |
| Video recording fails on macOS | Grant Cursor/Claude Code Screen Recording permission in System Settings → Privacy & Security. |
| Video on Windows shows the IDE instead of the app | Update to the latest CodeLoop. Multi-monitor focus is now first-class. |

Full troubleshooting guide: **[codeloop.tech/docs/faq](https://codeloop.tech/docs/faq)**

---

## Next steps

- 📚 **[Read the full docs](https://codeloop.tech/docs)**
- 🎯 **[Browse all MCP tools](https://codeloop.tech/docs/tools)**
- 💬 **[Join the community](mailto:support@codeloop.tech)** — Discord coming soon
- 🐛 **[Report a bug or request a feature](https://github.com/OrienjoJi/codeloop-mcp/issues)**
