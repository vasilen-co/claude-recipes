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

## Adding more external skills here

When cataloging additional external skills, record for each one:

- **Source URL** (deep-link to the `SKILL.md`, not just the repo).
- **Verbatim frontmatter** — `name`, `description`, `allowed-tools`. These drive trigger accuracy; paraphrasing them breaks discoverability.
- **When it's useful** — 3–5 concrete scenarios, not a feature dump. The catalog's value is helping a reader pick the right skill fast.
- **Install / permission caveats** — anything a consumer project has to allowlist or pre-install before the skill fires correctly.