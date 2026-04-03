# Cross-Tool Guide — Using These Concepts Beyond Claude Code

While this blueprint is built for Claude Code, the **principles are universal**. This guide maps each concept to its equivalent in other AI coding tools, based on their official documentation as of early 2026.

## Quick Reference: Config File Locations

| Tool | Behavioral Rules | Main Config | User Config Directory | Custom Agents / Skills | MCP Config |
|------|-----------------|-------------|----------------------|------------------------|------------|
| **Claude Code** | `CLAUDE.md` | `~/.claude/settings.json` | `~/.claude/` | `.claude/agents/*.md` + `~/.claude/skills/` | `.claude.json` or settings |
| **Cursor** | `.cursor/rules/*.mdc` + User Rules | `cli-config.json` · `permissions.json` | `~/.cursor/` | `~/.cursor/skills/` + built-in subagents | `~/.cursor/mcp.json` or Settings UI |
| **Codex CLI** | `AGENTS.md` | `~/.codex/config.toml` | `~/.codex/` | Via Agents SDK | In `config.toml` |
| **Gemini CLI** | `GEMINI.md` | `~/.gemini/settings.json` | `~/.gemini/` | Subagent configs | In `settings.json` |
| **Windsurf** | `.windsurf/rules/*.md` + `AGENTS.md` | `~/.codeium/windsurf/` | `~/.codeium/windsurf/` | Not supported | `mcp_config.json` |

**Cursor notes (read this if you use Cursor seriously):**

- **Rules** load from **two places**: repo `.cursor/rules/*.mdc` (often with `globs` / `alwaysApply`) and **global User Rules** in Cursor Settings. You do not have to duplicate everything in both -- use project rules for repo-specific conventions and User Rules for habits you want in every workspace.
- **Skills** (`~/.cursor/skills/<name>/SKILL.md`) are the closest analog to this blueprint’s **skills** folder for **Cursor’s native Agent** (natural-language triggers via the skill description). They are separate from `.mdc` rules.
- **Permissions**: `cli-config.json` controls **tool allowlists** for the Agent CLI; `permissions.json` provides **unified editor + CLI permissions** with sandbox modes. Neither is the same as VS Code’s `settings.json`. See [permissions docs](https://cursor.com/docs/reference/permissions).
- **Hooks** are configured in `hooks.json` (not `settings.json`). Cursor has ~11 hook types -- see the [Lifecycle Hooks](#3-lifecycle-hooks-claude-code--cursor--others-limited) section for the full list and CLI limitations.
- **`.cursorignore`** controls which files are excluded from Cursor’s codebase indexing -- like `.gitignore` for AI context. Add your memory/session files here if they shouldn’t be indexed.

---

## Feature Comparison Matrix

| Feature | Claude Code | Cursor | Codex CLI | Gemini CLI | Windsurf |
|---------|:-----------:|:------:|:---------:|:----------:|:--------:|
| Behavioral rules file | Yes | Yes | Yes | Yes | Yes |
| Rules hierarchy (global + project) | Yes | Yes | Yes | Yes | Yes |
| Path-scoped rules | Yes | Yes (globs in `.mdc`) | Yes (directory walk) | Yes (directory walk) | Yes (glob triggers) |
| Workflow “skills” (SKILL.md-style) | Yes (`~/.claude/skills/`) | Yes (`~/.cursor/skills/`) | Varies | Varies | Varies |
| Custom subagents | Yes | Yes (built-in types; skills for reusable workflows) | Yes | Yes (experimental) | No (parallel agents only) |
| Lifecycle hooks | Yes (broad: session, tool, compact, stop, …) | Yes (~11 event types; different surface -- see below) | Limited (prompt-level) | Yes (experimental) | Limited (enterprise audit) |
| MCP server support | Yes | Yes | Yes | Yes | Yes |
| Native memory persistence | Yes | Removed (use Rules / external patterns) | Transcript resume | Yes (`save_memory`) | Yes (auto-generated) |
| Model tiering per-agent | Yes | Mostly global / per-chat | Yes (per-config) | No | No |
| Permission / tool allowlist | Yes (`settings.json`) | Yes (`cli-config.json` + `permissions.json`; unified sandbox) | Yes (sandbox modes) | No | No |
| Worktree isolation | Yes | No | No | No | Yes (parallel agents) |

**Hooks -- different surfaces, not a subset:** Claude Code’s hooks (SessionStart, PreToolUse matchers, Pre/PostCompact, Stop) and Cursor’s hooks (~11 types including `beforeSubmitPrompt`, `afterAgentResponse`, `afterAgentThought`) cover **different events** -- each tool has hooks the other lacks. Cursor’s CLI currently only fires shell-related hooks; the full set requires the IDE. For **policy that must hold regardless of product**, prefer **git hooks** or **CI**; use each product’s hooks where they fit.

---

## Cursor in depth (for Claude Code users)

This section is **generic**: adapt paths, repo names, and policies to your own team. It does not assume any particular employer, stack, or private workflow.

### Same app, two configs (common confusion)

You can run **Claude Code inside Cursor** *and* use **Cursor’s own Agent / Chat**. They are different runtimes:

| Runtime | Typical config home | This blueprint maps to… |
|--------|---------------------|-------------------------|
| **Claude Code** (extension or CLI) | `~/.claude/` | Agents, skills, hooks, `settings.json` as documented in this repo |
| **Cursor native Agent** | `~/.cursor/` (+ Settings UI) | User Rules, `~/.cursor/skills/`, `cli-config.json`, `permissions.json`, `mcp.json` |

**Nothing auto-syncs** between those trees. If you want the same MCP servers in both, define them in **each** place (or script a small sync check). Same idea for “habit” rules: duplicate or split intentionally (global User Rules vs project `.mdc`).

### Skills on Cursor Agent

To reuse the **idea** of this blueprint’s skills (load-session, review, deploy-check, etc.) under Cursor’s Agent:

- Add folders under **`~/.cursor/skills/<skill-name>/SKILL.md`** with YAML frontmatter (`name`, `description`, …) as Cursor documents.
- **Optional:** On one machine, **directory junctions** (Windows) or **symlinks** (macOS/Linux) from `~/.cursor/skills/foo` → `~/.claude/skills/foo` avoid maintaining two copies — but **edits apply to both**; do not “fix for Cursor only” inside a shared file without affecting Claude Code.

### Permissions (`cli-config.json`)

Cursor's permission system has two layers: **`cli-config.json`** (tool allowlists for the Agent CLI) and **`permissions.json`** (unified editor + CLI permissions with sandbox modes). If the Agent refuses or always asks approval for basic work, your allowlist may be very narrow. Expanding `permissions.allow` (e.g. broader `Shell(*)` / read/write patterns) is a **conscious security and ergonomics tradeoff** -- pair with **git hooks** or team policy for anything that must never happen (e.g. pushes to protected branches). See [Cursor permissions docs](https://cursor.com/docs/reference/permissions) for sandbox modes and granular domain allowlisting.

### Hooks (`hooks.json`)

Cursor hooks are configured in a project-level or global `hooks.json` (not `settings.json`):

```json
{
  "version": 1,
  "hooks": {
    "beforeShellExecution": { "command": ["bash", "hooks/block-git-push.sh"] },
    "afterFileEdit": { "command": ["bash", "hooks/notify-file-changed.sh"] },
    "stop": { "command": ["bash", "hooks/security-check.sh"] }
  }
}
```

### When Claude hooks have no 1:1 equivalent

Some blueprint hooks (session injection, post-edit nudges, stop-time review) are **Claude Code-specific**. On Cursor you typically **combine**:

- **User Rules** -- e.g. “before finishing a change, self-check security and verification” (behavioral, not as strict as an external hook).
- **Git hooks** -- e.g. `pre-push` for branch policy (works for human and any AI).
- **Cursor hooks** (where available) -- wire only what your Cursor version supports; verify in official docs.
- **CLI limitation** -- if you use Cursor's CLI, only shell-related hooks fire. Other hooks (`afterFileEdit`, `stop`, `beforeSubmitPrompt`, etc.) require the IDE.

### Cross-session context without vendor “memory”

The [memory-template/](memory-template/) pattern (a **small private git repo** with markdown like `session.md`, decisions, reminders) works for **any** tool: any agent that can read files can load it when you ask. Automation differs: only your **skills / rules** define when that happens — there is no single universal auto-load unless you configure it per tool.

---

## Translating the Blueprint

### 1. Behavioral Rules (Every Tool Has This)

This is the most portable concept. Every AI coding tool reads a project-level instruction file.

**Claude Code** → `CLAUDE.md` in project root

**Cursor** → Combine as needed:

1. **Project:** `.cursor/rules/*.mdc` with frontmatter (`globs`, `alwaysApply`, `description`).
2. **Global:** Cursor **Settings → Rules** (User Rules) for habits you want in every repo.

```markdown
---
description: Verify after completing any implementation
globs: ["**/*.ts", "**/*.tsx"]
alwaysApply: true
---
After finishing any implementation, always run a verification step...
```

Note: `.cursorrules` (root file) is deprecated but still works. The `.mdc` format supports glob-scoped activation.

**Codex CLI** → `AGENTS.md` (hierarchical discovery)
Codex walks from `~/.codex/AGENTS.md` (global) down through project root to CWD, loading every `AGENTS.md` it finds. `AGENTS.override.md` takes precedence at each level.

**Gemini CLI** → `GEMINI.md` (hierarchical + imports)
Similar directory walk from `~/.gemini/GEMINI.md` (global) to project root (`.git` boundary). Supports `@file.md` imports for splitting large files into modules.

**Windsurf** → `.windsurf/rules/*.md` + `AGENTS.md`
Rules use frontmatter with `trigger` field (`glob`, `always_on`, `manual`). Root `AGENTS.md` files are always-on. Subdirectory `AGENTS.md` files auto-scope to that directory.

**What to copy:** Take the [CLAUDE.md](CLAUDE.md) template. Paste the rules into your tool's equivalent file(s). The content is tool-agnostic — only the file name, split, and format change.

---

### 2. Subagents / Custom Agents (Claude Code + Cursor + Codex + Gemini)

Claude Code lets you create specialized subagents with their own instructions, model, and tool access.

**Cursor** — two mechanisms:

1. **Built-in subagents** (e.g. review-focused tasks) invoked from the product UI / agent APIs -- check current Cursor docs for names and limits.
2. **`~/.cursor/skills/`** -- reusable workflows triggered by natural language; often easier for community members than maintaining many separate agent files.

**Codex CLI** supports subagents that spawn in parallel with independent context. Custom agents are configured via the OpenAI Agents SDK with model/instruction overrides.

**Gemini CLI** has experimental local and remote subagents with isolated context, independent history, and recursion protection.

**Windsurf** runs up to 5 parallel Cascade agents using Git worktrees (each gets its own branch), but does not support custom subagent definitions — they're full Cascade instances, not lightweight task-specific agents.

**What to copy:** The agent `.md` files from this blueprint can be adapted as **skill bodies** under `~/.cursor/skills/` or as instruction sets for Codex/Gemini subagents. The pattern -- separate concerns for architecture, implementation, review, and testing -- is tool-agnostic.

---

### 3. Lifecycle Hooks (Claude Code + Cursor — Others Limited)

Hooks are deterministic automation that fires on specific events (before/after file edits, shell commands, session start/end, etc.).

**Claude Code** has a broad hook system: SessionStart, PreToolUse, PostToolUse, PostToolUseFailure, PreCompact, PostCompact, Stop, SessionEnd — with matchers for specific tools.

**Cursor** has ~11 hook types configured via `hooks.json` (executables run outside the model): `beforeShellExecution`, `afterShellExecution`, `beforeMCPExecution`, `afterMCPExecution`, `beforeReadFile`, `afterFileEdit`, `beforeTabFileRead`, `afterTabFileEdit`, `beforeSubmitPrompt`, `afterAgentResponse`, `afterAgentThought`, and `stop`. Cursor also has hooks Claude Code lacks (e.g. `beforeSubmitPrompt`, `afterAgentResponse`). **CLI caveat:** the Cursor CLI currently only fires `beforeShellExecution` and `afterShellExecution`; other hooks require the IDE. Verify available hooks against [Cursor docs](https://cursor.com/docs/hooks) for your version.

**Codex CLI** has a `userpromptsubmit` hook that can block/augment prompts before execution. No file-edit or shell-execution lifecycle hooks.

**Gemini CLI** has experimental hooks for session context injection and active-agent tracking. Behind a toggle.

**Windsurf** has Cascade Hooks for logging and policy enforcement — enterprise/teams only, not general-purpose.

**What to copy:** The hook *scripts* in this blueprint (`protect-config.sh`, `block-git-push.sh`, etc.) can be repurposed as:

- **Git hooks** (`pre-push`, `post-commit`) that work with **any** editor or AI
- **Cursor hooks** where your version supports a matching event -- note that **Cursor CLI** currently only fires shell-related hooks; most others require the **IDE**
- **CI checks** for environments without editor hooks

---

### 4. Memory Persistence (Varies Significantly)

**Claude Code** has dual memory: auto-memory (`~/.claude/projects/*/memory/MEMORY.md`) + the git-backed pattern in this blueprint's [memory-template/](memory-template/).

**Cursor** briefly had a "Memories" feature but **removed it** starting v2.1.x. Users were told to export memories into Rules files. No built-in cross-session persistence. Community workarounds exist via MCP servers (Memory Banks, ContextForge) or a **private markdown/git “session log”** repo that agents read when instructed.

**Codex CLI** saves session transcripts to `~/.codex/history.jsonl`. `codex resume` reopens earlier threads with same state. This is transcript replay, not semantic memory.

**Gemini CLI** has a `save_memory` tool that appends facts to `~/.gemini/GEMINI.md` under a `## Gemini Added Memories` section. Loaded automatically in subsequent sessions. Note: this writes directly into the same file used for instructions — a known limitation.

**Windsurf** auto-generates memories during conversations, stored in `~/.codeium/windsurf/memories/`. Workspace-scoped, persists across sessions. Users can also write durable facts to `.windsurf/rules/` files.

**What to copy:** The [memory-template/](memory-template/) pattern works with **any** tool:

1. Create a **separate private** git repo for your memory data (not in your public blueprint fork if it contains secrets).
2. Teach your **rules or skills** to read/update agreed files (e.g. session summary, decisions) when you start or end work.
3. Only Claude Code can wire some of this to **hooks** automatically; elsewhere, a short **User Rule** or **skill** replaces “on session start, load X.”

---

### 5. Model Tiering (Partially Portable)

Claude Code lets you assign different models to different agents via frontmatter (`model: opus`, `model: sonnet`, `model: haiku`).

**Other tools:** Most let you select a model globally or per chat. The *principle* still applies:

- Use the strongest model for architecture and planning
- Use a balanced model for implementation and review
- Use the cheapest model for documentation

**Codex CLI** supports per-config model overrides, making it the closest equivalent.

---

### 6. Path-Scoped Rules (Most Tools Support This)

Loading different instructions for different parts of the codebase.

| Tool | Mechanism |
|------|-----------|
| **Claude Code** | `paths:` frontmatter in `.claude/rules/*.md` |
| **Cursor** | `globs:` in `.cursor/rules/*.mdc` |
| **Codex CLI** | Directory walk — `AGENTS.md` in any subdirectory scopes to that tree |
| **Gemini CLI** | Directory walk — `GEMINI.md` in subdirectories scopes automatically |
| **Windsurf** | `trigger: glob` in `.windsurf/rules/*.md` frontmatter |

---

## The Universal Takeaways

Regardless of which tool you use, these principles from the blueprint apply everywhere:

1. **Write your rules down.** Every tool has a rules file (and often global + project layers). Use them.
2. **Enforce, don't suggest.** Use hooks where they exist, **git hooks** or **CI** for rules that must hold no matter which AI or human runs the command.
3. **Scope your context.** Don't load everything into every session. Organize by domain (path-scoped rules, separate skill descriptions).
4. **Verify outputs.** Never trust "done" without checking the actual result. Tool-agnostic.
5. **Track decisions.** An append-only decision log prevents "why did we do this?" across sessions.
6. **Match model to task.** Use capable models for hard problems, cheaper models for routine work.
7. **Separate enforcement from guidance.** Things that MUST happen need hooks/CI. Things that SHOULD happen go in rules and skills.

---

*This guide reflects the state of these tools as of early 2026. AI coding tools evolve rapidly — features marked "experimental" may stabilize or change. Always confirm hook names, skill folders, and Settings paths against the vendor docs for your installed version.*
