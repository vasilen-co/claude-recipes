# claude-recipes

A catalog of Claude Code configuration recipes — agents, rules, and skills — that can be reused across projects.

## External skills

### playwright-cli (Microsoft)

Source: https://github.com/microsoft/playwright-cli/tree/main/skills/playwright-cli

A first-party Microsoft skill that gives Claude Code the ability to drive a real browser via the `playwright-cli` command. Installed into a project (copy the `skills/playwright-cli/` directory into `.claude/skills/`), it auto-triggers on browser-automation tasks.

**Frontmatter (verbatim):**

```yaml
name: playwright-cli
description: Automate browser interactions, test web pages and work with Playwright tests.
allowed-tools: Bash(playwright-cli:*) Bash(npx:*) Bash(npm:*)
```

**What it enables**

- **Navigation & interaction** — open pages, click, type, submit forms using snapshot refs (e.g. `e15`), CSS selectors, or Playwright locators (role-based, test-id).
- **State management** — cookies, localStorage / sessionStorage, persistent profile directories for session reuse across runs.
- **DevTools** — console output, network monitoring, tracing for debugging failing flows.
- **Multi-tab & multi-session** — named browser sessions, concurrent instances, CDP connections, browser extensions.
- **Cross-browser** — Chromium, Firefox, WebKit, Edge.
- **Pipeable output** — `--raw` strips status/snapshot sections so Claude can pipe results into other tools.

**When this skill is useful**

- Writing or fixing Playwright E2E tests — Claude can reproduce the failure in a live browser before editing the spec.
- Reproducing UI bugs from a user report without writing throwaway scripts.
- Scraping or inspecting authenticated pages where curl/fetch isn't enough.
- Verifying front-end changes end-to-end as part of a task's "done" check instead of assuming the UI works.
- Generating selector / locator candidates from a real snapshot rather than guessing from the DOM.

**Installation sketch**

1. Install the CLI: `npm i -g @playwright/cli` (or invoke via `npx playwright-cli`).
2. Copy `skills/playwright-cli/` from the upstream repo into your project's `.claude/skills/playwright-cli/`.
3. Confirm the `allowed-tools` entries match your project's permission settings — the skill needs `Bash(playwright-cli:*)`, `Bash(npx:*)`, `Bash(npm:*)` allowlisted.

## External tools

### ccinspect (Claude Code config linter)

Source: https://github.com/fedius01/ccinspect

A linter and runtime auditor for Claude Code configurations. Runs ~52 rules across settings, memory (CLAUDE.md), agents, rules, skills, commands, MCP servers, and git tracking — surfacing dead globs, contradictions, dangerous permissions, oversized files, stale imports, orphan agents/skills, and drift between static config and actual runtime behavior.

**Install / invoke**

- `npm install -g ccinspect` — auto-registers as an MCP server after a Claude Code restart, then invocable via natural language ("check my config for issues").
- Or run ad-hoc without installing: `npx ccinspect scan`.
- CLI subcommands: `cci scan | lint | audit | history | diff | restore`.

**When this tool is useful**

- Auditing a `.claude/` directory before sharing a recipe — catch orphan agents/skills and dead `tools:` references that silently break loading.
- Comparing static config to runtime behavior to find "write-blind" path-scoped rules or agents that never actually fire.
- Tracking token-budget growth as CLAUDE.md and rule files accumulate.
- Catching local-only files (e.g. `settings.local.json`) accidentally committed to git.
- Diffing config across branches or restoring a known-good prior state.

## Status line customization

Claude Code supports a custom status line via `settings.json` (`statusLine.type: "command"`). On each prompt, it runs the configured shell script, pipes session JSON (cwd, model, tokens, cost, transcript path) into it, and renders the script's stdout as the status line.

### Reference: wierdbytes/dotfiles

Source: https://github.com/wierdbytes/dotfiles/tree/master/home/dot_claude

A bash status line that reads the session JSON from stdin and renders four pipe-separated segments:

1. **Path** — abbreviated to `…/grandparent/parent/current` when long; parent in gray, current directory in purple.
2. **Git** — branch in cyan, with `✓` (clean) or `✗` (dirty).
3. **Context window** — percentage + 10-char progress bar; green `<60%`, yellow `60–80%`, red `>80%`. Also shows current tokens and tokens remaining before autocompaction.
4. **Session cost** — USD, hidden when zero.

Example output: `…/parent/directory │ branch-name ✓ │ 45%: 15k[▓▓▓▓░░░░░░]50k │ $0.25`

**Wiring it up** — add to `~/.claude/settings.json`:

```jsonc
{
  // ...your other settings...
  "statusLine": {
    "type": "command",
    "command": "/bin/bash ~/.claude/statusline-command.sh",
    "padding": 0
  }
}
```

Then drop the script at `~/.claude/statusline-command.sh` (chmod +x) and restart Claude Code.

**When this is useful**

- Watching context-window usage live to decide when to summarize or start a fresh session.
- Catching cost spikes mid-session before they surprise you.
- Surfacing git branch + dirty/clean state without leaving the Claude Code UI.
- A small, copy-pasteable starting point for teams that want a richer status line (model name, branch tracking info, MCP server status, etc.).

## Adding more external skills here

When cataloging additional external skills, record for each one:

- **Source URL** (deep-link to the `SKILL.md`, not just the repo).
- **Verbatim frontmatter** — `name`, `description`, `allowed-tools`. These drive trigger accuracy; paraphrasing them breaks discoverability.
- **When it's useful** — 3–5 concrete scenarios, not a feature dump. The catalog's value is helping a reader pick the right skill fast.
- **Install / permission caveats** — anything a consumer project has to allowlist or pre-install before the skill fires correctly.