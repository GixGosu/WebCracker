# 🔓 WebCracker

**An AI agent that autonomously reverse-engineers websites — analyzes source, learns how they work, and figures out how to navigate them without human guidance.**

---

## What It Does

WebCracker is a single markdown prompt ([`SOLVER.md`](./SOLVER.md)) that turns any AI coding agent into a security researcher. Point it at a web application and it will:

1. **Recon** — Download the JavaScript bundle, search for storage mechanisms, encryption patterns, hardcoded keys, and validation logic
2. **Analyze** — Map the attack surface: client-side vs server-side validation, state management, edge cases and bugs
3. **Exploit** — Generate a Playwright automation script targeting discovered vulnerabilities
4. **Execute** — Run the exploit, handle edge cases, iterate if needed

No hardcoded answers. No prior knowledge of the target. The agent discovers everything through analysis.

## The Story

A 30-step web security challenge at [`serene-frangipane-7fd25b.netlify.app`](https://serene-frangipane-7fd25b.netlify.app/) — puzzles, anti-bot measures, popups, Shadow DOM, WebSockets, Service Workers, canvas-rendered codes. The kind of thing that takes a human researcher hours of manual clicking and debugging.

We handed an AI agent `SOLVER.md` and the URL. It:

- Downloaded and grepped the JS bundle
- Found XOR encryption with a hardcoded key
- Discovered an off-by-one validation bug
- Found a timing check that passes on null
- Generated a Playwright exploit
- Completed 30/30 steps

**Time from "go" to done: under 2 minutes.** Zero human guidance after the initial prompt.

### What the agent discovered (on its own):

| Discovery | Details |
|-----------|---------|
| **Client-side encryption** | All 30 codes stored in `sessionStorage`, XOR-encrypted |
| **Hardcoded key** | `WO_2024_CHALLENGE` — found by grepping the bundle |
| **Off-by-one bug** | `validateCode(30)` checks `codes.get(31)` which doesn't exist |
| **Timing bypass** | Anti-bot check returns `true` if no start timestamp exists |
| **React form quirk** | Direct `input.value` doesn't trigger React state — needs synthetic events |

## Usage

Open a Claude session (Claude Code, Cursor, OpenClaw, or any AI agent with shell access) and say:

```
Follow the instructions in SOLVER.md step by step.
Target: https://your-target-url.com
```

The agent handles everything from there. It will:
- Fetch and analyze the target's source code
- Identify the fastest path to completion
- Generate and execute an exploit
- Iterate if the first attempt fails

### Requirements

- An AI coding agent with shell access (Claude Code, Cursor, OpenClaw, etc.)
- Node.js 18+ (for generated Playwright scripts)
- `npx playwright install chromium` (one-time browser setup)

## How SOLVER.md Works

The prompt is a 5-phase workflow:

**Phase 1: Reconnaissance** — Download the JS bundle. Run targeted grep patterns for storage keys, encryption functions, validation logic, hardcoded constants. Extract surrounding context using byte-offset reads (handles minified bundles).

**Phase 2: Analysis** — Answer five questions: Where is state stored? What protects it? Is validation client-side? Are there edge case bugs? What's the fastest path?

**Phase 3: Exploit Generation** — Based on findings, generate a Playwright script. Two strategy templates: direct state manipulation (if client-side only) or full browser automation (if server-validated).

**Phase 4: Execution** — Save and run the generated script.

**Phase 5: Iteration** — If it fails, analyze the error, fix, and retry. Most targets fall in 1-2 iterations.

## Project Structure

```
WebCracker/
├── README.md        # You're here
├── SOLVER.md        # The autonomous agent prompt — this is the tool
└── screenshots/
    ├── success.png        # 30/30 completion proof
    └── before-submit.png  # Step 30 with code filled
```

## Limitations

- **Requires shell access** — The agent needs to run commands. Won't work in chat-only interfaces.
- **Target-specific results** — The specific vulnerabilities found are unique to each target. The *methodology* is general.
- **Minified code analysis** — Heavily obfuscated bundles may need more iterations. The grep patterns handle standard minification well.

## Credits

Built by **[@brineshrimp](https://github.com/GixGosu)**

One prompt. One URL. Zero hand-holding.
