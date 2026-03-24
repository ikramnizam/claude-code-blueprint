# Claude Code Blueprint

A battle-tested reference architecture for Claude Code power users. Not a plugin to install -- a blueprint to learn from and adapt.

## What This Is

This repository documents a production Claude Code setup built over 65+ sessions of real development work. Every agent, skill, hook, and rule exists because a real incident taught us it was needed.

**This is NOT a generic starter kit.** It's a reference architecture showing how a power user configures Claude Code for maximum productivity, with the reasoning behind every decision.

## What's Inside

| Component | Count | Purpose |
|-----------|-------|---------|
| **Agents** | 11 | Specialized subagents with model tiering (opus/sonnet/haiku) |
| **Skills** | 15 | Natural-language-triggered workflows (no slash commands needed) |
| **Hooks** | 11 | Deterministic lifecycle automation (10 hook events) |
| **Rules** | 4 | Path-scoped behavioral constraints |
| **Output Styles** | 4 | Context-switching personas (architect, DBA, DevOps, security) |
| **Memory System** | Dual | Auto-memory + external git-backed persistence |
| **CLAUDE.md** | Template | Battle-tested behavioral rules |

## Philosophy

1. **Hooks for enforcement, CLAUDE.md for guidance** -- Hooks fire 100% of the time. CLAUDE.md instructions are followed ~80%. If something MUST happen, make it a hook.

2. **Agent-scoped knowledge, not global bloat** -- Design principles live in the frontend agent, not in every session's context. Security patterns live in the security-reviewer, not in CLAUDE.md.

3. **Context is currency** -- Every token loaded into context is a token not available for your code. Keep MEMORY.md under 100 lines. Extract to topic files. Use path-scoped rules so irrelevant rules don't load.

4. **Battle-tested over theoretical** -- Every rule in this repo exists because something went wrong without it. The "WHY" matters more than the "WHAT".

## Quick Start

1. Clone this repo
2. Copy the components you need into your `~/.claude/` directory
3. Read `WHY.md` to understand the reasoning behind each component
4. Adapt the templates to your project's conventions
5. Start with CLAUDE.md + 2-3 hooks, then add agents/skills as needed

## Architecture

See [ARCHITECTURE.md](ARCHITECTURE.md) for the full system design, component relationships, and hook lifecycle diagram.

## Battle Stories

See [WHY.md](WHY.md) for the incidents and lessons behind every component. This is the most valuable file in the repo.

## License

MIT
