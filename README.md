<div align="center">

<img src="docs/assets/codeloop-logo.svg" alt="CodeLoop" width="320" />

# CodeLoop

### Automated verification, self-repair, and confidence gating for AI coding agents.

**Stop manually testing AI-generated code.** CodeLoop is an MCP server that plugs into Cursor and Claude Code and turns the back-and-forth between you, your AI agent, and your bug list into a closed, automated loop — *Build → Verify → Diagnose → Fix → 94% confidence gate → ship.*

[**Start free trial →**](https://codeloop.tech/signup) &nbsp;·&nbsp; [**Pricing**](https://codeloop.tech/pricing) &nbsp;·&nbsp; [**Docs**](https://codeloop.tech/docs) &nbsp;·&nbsp; [**Blog**](https://codeloop.tech/blog) &nbsp;·&nbsp; [**Earn 14 free days →**](CONTRIBUTOR_REWARDS.md)

[![npm version](https://img.shields.io/npm/v/codeloop?label=codeloop&color=536bb2)](https://www.npmjs.com/package/codeloop)
[![npm version](https://img.shields.io/npm/v/codeloop-mcp-server?label=codeloop-mcp-server&color=536bb2)](https://www.npmjs.com/package/codeloop-mcp-server)
[![License](https://img.shields.io/badge/license-Proprietary-lightgrey)](#license)
[![Website](https://img.shields.io/badge/codeloop.tech-live-brightgreen)](https://codeloop.tech)

</div>

---

## The problem

You ask Cursor or Claude Code to build a feature. The AI writes code. Then **you** become the QA team:

- You manually open the simulator, click around, and check the UI.
- You re-run the tests by hand.
- You eyeball screenshots and compare them to the design.
- You write the bug report back into the chat.
- The agent fixes one thing, breaks another, and you start over.

**For every feature, you do 5–10 manual verification cycles before it's merge-ready.**

## The solution

CodeLoop turns that manual cycle into an automatic one. Once installed, your AI agent gains a set of MCP tools — `codeloop_verify`, `codeloop_diagnose`, `codeloop_interact`, `codeloop_visual_review`, `codeloop_gate_check`, and more — and a strict rule set that forces it to use them after every change.

```text
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│   Agent writes code                                             │
│         │                                                       │
│         ▼                                                       │
│   codeloop_verify ──► tests, builds, screenshots, video         │
│         │                                                       │
│         ▼                                                       │
│   codeloop_interact ─► click, type, scroll, login, submit...    │
│         │                                                       │
│         ▼                                                       │
│   codeloop_diagnose ─► repair tasks with file scope + evidence  │
│         │                                                       │
│         ▼                                                       │
│   Agent fixes the issues ──┐                                    │
│         │                  │  Auto-loop until 94% confidence    │
│         ▼                  │  (no human prompting required)     │
│   codeloop_gate_check ─────┘                                    │
│         │                                                       │
│         ▼                                                       │
│   ✅  ready_for_review  →  human takes a final look             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

You only get pulled in **at the end**, when the build is already at 94%+ confidence. Everything before that is the agent talking to CodeLoop.

---

## What you get

### One verification loop, every platform

| You're building... | On macOS | On Windows | On Linux |
|---|:---:|:---:|:---:|
| iOS app (Swift / SwiftUI / Flutter) | ✅ | — | — |
| Android app (Kotlin / Java / Flutter) | ✅ | ✅ | ✅ |
| macOS desktop (Swift / Flutter / Electron) | ✅ | — | — |
| Windows desktop (.NET / WPF / Flutter) | — | ✅ | — |
| Linux desktop (Flutter / Electron) | — | — | ✅ |
| Web app (React, Next.js, Vue, Svelte, …) | ✅ | ✅ | ✅ |
| Flutter cross-platform | ✅ all targets | ✅ Win/Android/Web | ✅ Linux/Android/Web |

CodeLoop **auto-detects** which build tools, simulators, and emulators are available on your machine and runs only the checks that can succeed. iOS missing because you're on Windows? It's reported as *skipped*, not failed — and your build keeps moving.

### A 40+ action interaction engine

Most "AI test" tools can fill a form. CodeLoop drives a **real user**.

- **Web (Playwright headed Chromium):** click, double-click, right-click, hover, type, fill, submit, hotkey, scroll, drag, select, upload, navigate, back/forward, wait, sequence
- **Desktop (macOS · Windows · Linux):** native click, type, hotkey, focus app, multi-monitor window targeting, Windows UI Automation tree inspection
- **Android (adb + emulator):** tap, swipe, type, back, home, deep-link, grant permission, rotate, mock location, simulate network
- **iOS (xcrun simctl):** tap, swipe, type, biometric auth, push notification, launch/clear app
- **Maestro flows** for declarative mobile journeys

Every interaction is logged into a structured `interaction_log.jsonl` with input arguments, success/failure, browser console errors, and a screenshot reference — so the agent (and you) can debug exactly what happened.

### Real video, real screenshots, brought to the front

CodeLoop records video of every test run, captures screenshots on demand, and **brings the target app to the front of the user's preferred monitor** before recording — so you never end up with a video of the IDE instead of the app you were testing. Multi-monitor setups are first-class.

Per platform:

- **macOS:** `ffmpeg avfoundation` + `screencapture -v` fallback, with active-screen detection
- **Windows:** `ffmpeg gdigrab` with the target app's monitor
- **Linux:** `ffmpeg x11grab`
- **Android:** `adb screenrecord`
- **iOS:** `xcrun simctl io recordVideo`
- **Web:** Playwright video + per-step screenshots

### App + browser logs, automatically captured

While recording, CodeLoop also pulls down the relevant logs in parallel — Flutter logs, `adb logcat`, `simctl log stream`, macOS `log stream`, browser console (incl. errors, warnings, page errors, failed requests, HTTP ≥ 400), and dev-server stdout/stderr. The agent reads these alongside the video to spot bugs you'd miss with your eyes.

### A 94% confidence gate that actually blocks

The MCP server enforces a strict gate before any work is handed back to you:

- **Required evidence:** screenshot, video, interaction replay, and interaction-coverage check against discovered routes
- **Quality gates:** build, lint, type-check, unit tests, integration tests, visual review, design comparison
- **Confidence threshold:** 94%
- **Below threshold:** the agent receives a `MANDATORY AUTO-FIX LOOP` directive and is forced to verify → diagnose → fix → re-verify, **without asking you**, for up to 15 iterations.

Only when the gate returns `ready_for_review` is your attention requested.

### A full development log, written for you

When the loop completes, `codeloop_generate_dev_report` produces a `docs/DEVELOPMENT_LOG.md` containing the run history, every interaction performed, console errors observed, gates that passed/failed, baselines updated, and the final confidence breakdown. It's the kind of doc your team would never write by hand.

---

## Quick start (60 seconds)

### 1. Get an API key

Sign up for a 14-day free trial at **[codeloop.tech/signup](https://codeloop.tech/signup)** — no credit card required. You'll get an API key that looks like `cl_live_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.

> Or skip the browser entirely: `npx codeloop signup` will create your account from the terminal.

### 2. Install CodeLoop in your project

```bash
cd your-project
npx codeloop init
```

That single command:

- Detects your stack (Flutter, Next.js, Vue, Xcode, .NET, Android Studio, …)
- Creates `.codeloop/config.json`
- Configures **both** Cursor (`.cursor/mcp.json` + rules) and Claude Code (`.claude/settings.local.json` + agent instructions) if it finds them
- Sets up the artifacts directory and spec/acceptance templates
- Adds itself to your `.gitignore`

If you already have a key, paste it when prompted. If not, the CLI offers inline signup so you never have to leave the terminal.

> **Already have a key?** `npx codeloop init --key cl_live_...` skips the prompt entirely.

### 3. Restart your IDE → done

Reopen Cursor or restart Claude Code. The `codeloop_*` tools appear in your agent's tool list, the rules are loaded, and the loop is armed.

Now ask your agent something like:

> "Implement the login screen with email/password validation."

The agent will write the code, then **automatically** call `codeloop_verify`, `codeloop_interact`, and `codeloop_gate_check`, fix anything broken, and only come back to you when the build hits 94% confidence.

---

## Setup paths

CodeLoop supports four onboarding paths — all converge on the same backend, the same key, and the same loop:

| Path | Command | Best for |
|---|---|---|
| **Website signup** | Visit [codeloop.tech/signup](https://codeloop.tech/signup) → copy key → `export CODELOOP_API_KEY=...` | Discovered CodeLoop via the web |
| **CLI signup** | `npx codeloop signup` | Terminal-first developers |
| **Inline (recommended)** | `npx codeloop init` | First-time setup in a new project |
| **Agent-guided** | Ask your agent: "Install CodeLoop." It runs `npx codeloop init` for you | Already in Cursor / Claude Code |

Detailed walk-throughs for each: **[codeloop.tech/docs/quickstart](https://codeloop.tech/docs/quickstart)**

---

## MCP tools your agent gets

| Tool | What it does |
|---|---|
| `codeloop_init_project` | Scaffolds CodeLoop config for the current project |
| `codeloop_verify` | Runs build + tests + checks; returns structured report |
| `codeloop_diagnose` | Classifies failures into repair tasks with file scope + evidence |
| `codeloop_interact` | Drives 40+ interactions across web / desktop / Android / iOS |
| `codeloop_start_recording` / `codeloop_stop_recording` | Captures video + app logs of a session |
| `codeloop_capture_screenshot` | Multi-viewport screenshots, brought-to-front |
| `codeloop_record_interaction` | Single-shot record of one interaction |
| `codeloop_interaction_replay` | Extracts frames + analyzes a recorded flow |
| `codeloop_visual_review` | Vision-model audit of screenshots (delegated to your agent's model) |
| `codeloop_design_compare` | Compares implementation screenshots against a design reference |
| `codeloop_discover_screens` | Parses your source for routes/screens to test |
| `codeloop_check_workflow` | Enforces evidence requirements + interaction coverage |
| `codeloop_gate_check` | 94% confidence gate; auto-loops fixes if below |
| `codeloop_section_status` | Multi-section progress tracking |
| `codeloop_release_readiness` | Full-app, all-platform pre-release report |
| `codeloop_generate_dev_report` | Writes `docs/DEVELOPMENT_LOG.md` from run history |
| `codeloop_recommend_tool` | Stack-aware library/service recommendations |
| `codeloop_recommend_action` | Suggests the agent's next move |
| `codeloop_integration_check` | Cross-section regression check |
| `codeloop_update_baseline` | Updates accepted visual baselines |
| `codeloop_replan` | Triggers replanning when scope changes |

Full reference with parameters and example outputs: **[codeloop.tech/docs/tools](https://codeloop.tech/docs/tools)**

---

## Pricing

| Plan | Price | What's included |
|---|---|---|
| **Trial** | Free for 14 days | Everything. No credit card. 200 verifications, 100 visual reviews, 50 design comparisons. |
| **Solo** | **$5 / month** | Unlimited verifications, 1,000 visual reviews, 500 design comparisons, 1 user, 3 keys. |
| **Team** | **$19 / month** | Unlimited verifications, 5,000 visual reviews, 2,000 design comparisons, 10 users, 10 keys. |
| **Enterprise** | **$49 / month** | Unlimited everything, priority + Slack support, unlimited team members and keys. |

Full feature comparison and FAQ: **[codeloop.tech/pricing](https://codeloop.tech/pricing)**

---

## How CodeLoop is different

| | Manual QA | Generic test runners | **CodeLoop** |
|---|:---:|:---:|:---:|
| Runs without you opening the app | ❌ | ✅ | ✅ |
| Drives real user interactions (login, type, submit, hotkey, swipe…) | ✅ | partial | ✅ |
| Captures video + screenshots + app logs in one shot | ❌ | partial | ✅ |
| Brings the app to the front on the right monitor | ❌ | ❌ | ✅ |
| Works on iOS, Android, macOS, Windows, Linux, web | depends | depends | ✅ |
| Plugs directly into Cursor and Claude Code as MCP tools | ❌ | ❌ | ✅ |
| Forces the agent to fix issues without asking the user | ❌ | ❌ | ✅ |
| Enforces a confidence gate before handing back to the human | ❌ | ❌ | ✅ |
| Produces a full development log automatically | ❌ | ❌ | ✅ |

---

## FAQ

**Is CodeLoop open source?**
The MCP server, CLI, and website are a closed-source commercial product. This repository is for product information and setup instructions. Source code is hosted privately. The CodeLoop MCP server itself is distributed via npm.

**Does CodeLoop send my code to a server?**
No. The MCP server runs **locally** on your machine. The only call to our backend is API key validation (cached for 60 seconds) and anonymous usage metering (counts only — no source, screenshots, or logs).

**Do I need a separate vision API key?**
No. Visual review is delegated to your agent's existing vision model (the one already in Cursor or Claude Code). CodeLoop just hands it the screenshots as MCP `ImageContent` blocks and the structured questions to answer.

**What if I'm offline?**
CodeLoop uses cached key validation (60s TTL) and an offline mode that lets local verification continue. Usage events queue and sync when you're back online.

**Will it work with my framework?**
If you're building anything for iOS, Android, macOS, Windows, Linux, or the web — yes. CodeLoop auto-detects Flutter, React, Next.js, Vue, Svelte, Xcode (Swift/SwiftUI), Android Studio (Kotlin/Java), .NET (C#/WPF), and falls back to a generic web/desktop runner for everything else.

**Can I self-host?**
Self-hosted backend is on the roadmap for Enterprise plans. Contact us at [support@codeloop.tech](mailto:support@codeloop.tech).

More questions: **[codeloop.tech/docs/faq](https://codeloop.tech/docs/faq)**

---

## 🎁 Contributor Rewards Program — earn 14 free days

Spotted a bug? Have an idea that would make CodeLoop better? Send it in. Every accepted submission earns you **14 days of CodeLoop on us** — your trial is extended, or 14 days of credit are added to your next billing period.

- Submit from the dashboard: [codeloop.tech/dashboard/contribute](https://codeloop.tech/dashboard/contribute)
- Or email [support@codeloop.tech](mailto:support@codeloop.tech?subject=%5BContribute%5D%20) with `[Contribute]` at the start of the subject line

Full details: [**CONTRIBUTOR_REWARDS.md**](CONTRIBUTOR_REWARDS.md)

---

## Resources

- 🌐 **Website:** [codeloop.tech](https://codeloop.tech)
- 📚 **Docs:** [codeloop.tech/docs](https://codeloop.tech/docs)
- 💳 **Pricing:** [codeloop.tech/pricing](https://codeloop.tech/pricing)
- 📝 **Blog:** [codeloop.tech/blog](https://codeloop.tech/blog)
- 📦 **CLI on npm:** [npmjs.com/package/codeloop](https://www.npmjs.com/package/codeloop)
- 📦 **MCP server on npm:** [npmjs.com/package/codeloop-mcp-server](https://www.npmjs.com/package/codeloop-mcp-server)
- ✉️  **Support:** [support@codeloop.tech](mailto:support@codeloop.tech)

---

## License

CodeLoop is a proprietary commercial product. The contents of this repository are licensed under the [CodeLoop Marketing Materials License](LICENSE) — you may share screenshots, quotes, and links freely, but you may not redistribute or modify the materials as your own product.

The CodeLoop MCP server, CLI, backend, and website source code are not open source.

© 2026 CodeLoop. All rights reserved.
