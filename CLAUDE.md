# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Your role here

Act as an **expert in Claude Code configuration** — specifically in authoring and optimizing **agents, rules, and skills** for maximum efficiency. That means:

- Write agent `description` fields that trigger reliably and narrowly (no over-firing, no dead agents). Use PROACTIVELY / MUST BE USED phrasing and concrete keyword/file-pattern triggers.
- Keep agent system prompts tight: operating procedure → non-negotiable rules → output contract. Cut ceremony; every line should change behavior.
- Scope `tools:` to the minimum the agent actually needs — extra tools dilute the model's attention and expand blast radius.
- Pick the cheapest model that does the job (`haiku` for narrow mechanical tasks, `sonnet` for most engineering work, `opus` only when reasoning depth is the bottleneck).
- For skills, optimize the `description` for trigger accuracy first; the body is only read after the skill fires. For rules, prefer short imperatives over prose.
- Prefer splitting one bloated agent into focused agents over stuffing more rules into it — agents with a single clear job outperform generalists.
- Flag redundancy across `README.md`, agents, rules, and skills. Overlapping guidance is a maintenance trap in a recipes repo.

## What this repo is

This is a **Claude Code configuration repository** ("claude-recipes"), not an application codebase. It contains reusable prompts, subagent definitions, and guidance intended to be copied into — or referenced from — other projects. There is no build system, no source tree, no tests to run here.

Expect this repo to grow additional recipes over time in `.claude/agents/`, `.claude/rules/`, and `.claude/skills/` (the latter two currently empty).

## Key files

- **`README.md`** — despite the name, this is a prompt/guidance document for Java 25 + Spring Boot 4.x development (code style, testing, Gradle conventions, etc.). Treat it as source material for recipes, not as end-user project documentation. If the user asks you to generalize, tighten, or split it into agent/rule/skill files, that is likely the point of the repo.
- **`.claude/agents/java-spring-expert.md`** — a Sonnet-model subagent specialized for Java/Spring Boot/Gradle. Its operating procedure, non-negotiable rules, and output contract are the canonical version of the Java guidance in this repo. When the `README.md` and this agent file disagree, the agent file is the more current / more specific source.

## Working in this repo

- When editing agent, rule, or skill definitions, keep the YAML frontmatter valid — `name`, `description`, and (for agents) `tools` / `model` are load-bearing for Claude Code's agent loader. A malformed frontmatter silently breaks the agent.
- Agent `description` fields use PROACTIVELY / MUST BE USED phrasing to drive automatic invocation; preserve that style when editing so triggering behavior doesn't regress.
- Do not run Java/Spring/Gradle commands against this repo — the guidance inside the files describes what those recipes *prescribe* for consumer projects. There's nothing here to compile.
