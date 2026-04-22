# CodeLoop Features

Everything CodeLoop does for you, in detail.

---

## 1. The verify → diagnose → fix → gate loop

CodeLoop's core idea is a **closed loop** that runs entirely between your AI agent and the MCP server, with no human in the middle until the work is at 94% confidence.

```text
Agent writes code
   ↓
codeloop_verify     ──► build, tests, screenshots, video, logs
   ↓
codeloop_interact   ──► click, type, login, submit, scroll
   ↓
codeloop_diagnose   ──► structured repair tasks with file scope + evidence
   ↓
Agent applies fixes
   ↓                                    Repeat up to 15 times
codeloop_gate_check ──► < 94%? ─── Yes ──┐ (no human prompt)
   ↓                                     │
   ≥ 94%                                 │
   ↓                                     │
ready_for_review  ←──────────────────────┘
```

The 94% threshold is enforced server-side. The agent is given a `MANDATORY AUTO-FIX LOOP` directive whenever the gate fails, so it physically cannot stop and ask you what to do.

---

## 2. 40+ interaction actions across every platform

Form-filling alone doesn't catch real bugs. CodeLoop lets your agent drive the app like a real user across:

### Web (Playwright headed Chromium)

| Action | Use case |
|---|---|
| `click`, `double_click`, `right_click`, `hover` | Buttons, menus, context menus, tooltips |
| `type`, `fill`, `submit` | Login forms, signups, search bars |
| `hotkey` | Keyboard shortcuts, modal close (Esc), undo (Cmd+Z) |
| `scroll`, `drag`, `select_option`, `toggle` | Lists, sliders, dropdowns, switches |
| `upload_file` | File pickers |
| `navigate_url`, `navigate_back`, `navigate_forward`, `wait` | Multi-page flows |
| `sequence` | Compose any of the above into a single named scenario |
| `fill_form` | Auto-fill an entire form by selector→value map |

### Desktop (macOS, Windows, Linux)

| Action | macOS | Windows | Linux |
|---|:---:|:---:|:---:|
| `click`, `double_click`, `right_click`, `hover` | ✅ CGEvent / Swift | ✅ user32.dll + UI Automation | ✅ xdotool |
| `type`, `hotkey` | ✅ | ✅ PowerShell | ✅ |
| `focus_app`, `bring_to_front` | ✅ osascript | ✅ ShowWindow + SetForegroundWindow | ✅ wmctrl |
| `win_ui_inspect`, `win_ui_automate` | — | ✅ Windows UI Automation tree | — |
| Multi-monitor targeting | ✅ active screen detection | ✅ monitor-aware | ✅ |

### Android (adb + emulator)

`tap`, `swipe`, `type`, `back`, `home`, `deep_link`, `grant_permission`, `rotate_device`, `mock_location`, `simulate_network`, `launch_app`, `clear_app_data`

### iOS (xcrun simctl)

`tap`, `swipe`, `type`, `biometric_auth`, `launch_app`, `clear_app_data`, `push_notification`

### Mobile flows

`maestro_flow` runs a declarative Maestro YAML for repeatable mobile journeys.

---

## 3. Real video recording, brought to the front

When `codeloop_start_recording` runs, CodeLoop:

1. **Detects the target app's window** and which monitor it's on.
2. **Brings that window to the front** of the user's preferred (most-recently-used) monitor.
3. **Starts recording on that monitor**, not the IDE's monitor.
4. **Captures app logs in parallel** (Flutter, adb logcat, simctl, macOS log stream, browser console, dev server stdout/stderr).
5. **Restores focus to the IDE** when recording ends.

| Platform | Recorder | Notes |
|---|---|---|
| macOS | `ffmpeg avfoundation` | Falls back to `screencapture -v` if ffmpeg missing |
| Windows | `ffmpeg gdigrab` | Multi-monitor aware |
| Linux | `ffmpeg x11grab` | |
| Android | `adb screenrecord` | Via emulator |
| iOS | `xcrun simctl io recordVideo` | Via simulator |
| Web | Playwright video | Plus per-step screenshots |

Output: `artifacts/runs/<run_id>/videos/*.mp4` + `logs/*.log` + `screenshots/*.png`.

---

## 4. Multi-viewport, multi-platform screenshots

`codeloop_capture_screenshot` takes screenshots at multiple viewport sizes in one call:

- **Web:** mobile (375×812), tablet (768×1024), desktop (1280×800), full-page
- **iOS / Android:** all configured simulator sizes
- **Desktop:** the active app window, framed

Each screenshot includes metadata — viewport, device pixel ratio, URL/route, timestamp — and is referenced from the run's `meta.json` so the agent can correlate screenshots with interactions.

---

## 5. Visual review (delegated to your agent's vision model)

`codeloop_visual_review` returns the captured screenshots as MCP `ImageContent` blocks **plus** a structured set of questions for the agent's vision model to answer:

- Does the layout match the description?
- Are there visible glitches, overlapping elements, missing assets?
- Is text readable? Is contrast sufficient?
- Are touch targets large enough?
- Does the empty/loading/error state look correct?

You don't pay for a separate vision API. The model already in your IDE (Claude, GPT-4o, Gemini, etc.) does the work.

---

## 6. Design comparison

`codeloop_design_compare` takes a reference image (Figma export, mockup screenshot, design system component) and the captured implementation screenshot, then returns:

- A pixel-diff overlay
- Spacing / alignment / colour discrepancies
- A delegated visual review prompt for the agent's model to reason about

Baselines are governed: `codeloop_update_baseline` is the only way to accept a new look-and-feel as the new normal.

---

## 7. Interaction coverage check

`codeloop_check_workflow` doesn't just check that you ran the tests. It enforces:

- **Screenshot evidence** present
- **Video evidence** present
- **Interaction replay evidence** present
- **Interaction coverage**: did the agent actually visit and interact with the routes/screens that `codeloop_discover_screens` parsed from the source?

If your app has 8 routes and the agent only interacted with 3, `check_workflow` returns `PENDING` and lists the uncovered routes. The agent must navigate to them and exercise their elements before the gate will pass.

---

## 8. Structured interaction logs

Every action goes into `artifacts/runs/<run_id>/logs/interaction_log.jsonl` as a single line of JSON:

```json
{
  "timestamp": "2026-04-17T11:42:08.183Z",
  "action": "click",
  "target_type": "browser",
  "success": true,
  "detail": "Clicked button[type=submit]",
  "input_args": { "selector": "button[type=submit]", "url": "https://localhost:3000/login" },
  "console_errors": [
    { "type": "pageerror", "text": "TypeError: Cannot read property 'token' of undefined" }
  ],
  "screenshot_ref": "screenshots/login_after_submit_375x812.png"
}
```

The agent reads this back during `diagnose` to correlate console errors with interactions, and `generate_dev_report` aggregates it into a human-readable interaction history.

---

## 9. Cross-platform auto-detection

CodeLoop never refuses to run because something's missing. It:

- Detects your OS (`darwin` / `win32` / `linux`)
- Probes for available tools (`flutter doctor`, `xcode-select`, `which maestro`, `npx playwright --version`, Android SDK, .NET SDK, etc.)
- Runs the checks that **can** succeed on this machine
- Reports the rest as `skipped` with the reason

So a Windows developer building a Flutter app gets:

> ✅ flutter analyze · ✅ flutter test · ✅ Android emulator tests
> ⊘ iOS skipped — requires macOS with Xcode

…and the build keeps moving.

---

## 10. Multi-section orchestration

For larger apps, `codeloop_section_status` tracks every section through a strict state machine:

```text
planned → ready_to_build → building → verifying →
{ needs_repair → building → verifying } * ≤ N →
internally_verified → integration_pending → done
```

Sections that pass individually but break later are flagged as `integration_regression` and re-enter the loop. The agent uses `codeloop_replan` when scope changes.

When all sections are `done`, `codeloop_release_readiness` produces the final, multi-platform pre-release report.

---

## 11. Auto-generated development log

`codeloop_generate_dev_report` writes a `docs/DEVELOPMENT_LOG.md` containing:

- Every run, with timestamps, gates, and confidence
- The full interaction history (last 50 entries by default)
- Aggregated counts: total interactions, succeeded, failed, action breakdown, console errors observed
- Baselines updated, new specs written, scope changes
- The final release readiness summary

Your team gets the kind of changelog they'd never write by hand.

---

## 12. Stack-aware recommendations

`codeloop_recommend_tool` is a recommendation engine for AI agents:

> "I need to send a password reset email from a Node.js / Heroku backend with a custom domain — what should I use?"

…returns a ranked list with rationale (e.g. Mailgun via SMTP because Heroku already supports it, has a free tier, supports custom domains, etc.). The agent presents the top recommendation to you and applies it.

---

## 13. Works with both Cursor and Claude Code (full parity)

Same MCP server, same tools, same loop, same rules. Cursor users get `.cursor/rules/*.mdc` and Cursor skills. Claude Code users get `.claude/agents/*.md`, agent instructions, and slash commands. Behaviour is identical: the agent in either IDE will exercise every interactive element, capture video on the right monitor, hit the 94% gate, and write the dev report.

---

## 14. Privacy & security

- **Your code stays on your machine.** The MCP server is local. The only call to our backend is API key validation (cached 60s) and anonymous usage counts.
- **API keys are SHA-256 hashed** before storage. We never see the raw key after issuance.
- **`.codeloop/config.json` is auto-added to `.gitignore`** so your key isn't accidentally committed.
- **All backend traffic is HTTPS.**
- **Offline mode** allows local verification to continue without backend connectivity.

Full security details: **[codeloop.tech/docs/api-keys](https://codeloop.tech/docs/api-keys)**

---

For pricing and signup, head to **[codeloop.tech/pricing](https://codeloop.tech/pricing)**.
