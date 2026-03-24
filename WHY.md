# WHY — Battle Stories Behind Every Decision

Every component in this blueprint exists because something went wrong without it. This document captures the incidents, the lessons, and the rationale.

> "The setup encodes hard-won lessons from real incidents. The only thing stopping us is if we are stale and not making any developments." — Session 66

<!-- TODO: Populate with sanitized battle stories from sessions 20-65 -->
<!-- Each section should follow: What Happened → What We Learned → What We Built -->

## Hooks

### Stop Hook: Why Sonnet, Not Haiku
<!-- Session 52: haiku missed a SQL injection pattern in a 500-word security prompt -->

### PostCompact Hook: Why State Serialization
<!-- Long sessions lost critical context after auto-compaction — prompt-only injection wasn't enough -->

### Config Protection Hook: Why It Exists
<!-- Claude "fixed" a lint error by disabling the lint rule instead of fixing the code -->

### Block-Git-Push Hook: Why Manual Pushes
<!-- Accidental push triggered CI/CD pipeline, disrupted team mid-pull -->

## Agents

### Model Tiering: Why Not All Opus
<!-- Cost optimization: docs-writer on haiku saves ~70% with no quality loss on prose tasks -->

### Worktree Isolation: Why Review Agents Need Fresh Context
<!-- Same-context self-review has blind spots — verify-plan caught 4 issues after 3 rounds of in-context review missed them -->

### permissionMode: plan for Read-Only Agents
<!-- db-analyst accidentally modified a file when it was only supposed to analyze -->

## Rules

### Plan-First Rule: Why Human Review Before Execution
<!-- Shipped 3 wrong implementations in one session before realizing review-before-code saves time -->

### Verify-After-Complete: Why "Done" Requires Proof
<!-- A 200 response returning { weeks: [], totalContributions: 0 } looked like success but was a silent failure -->

### Diagnose-First Rule: Why Check Git Before Investigating
<!-- Built an elaborate fix plan for a "missing file" that was just an unstaged deletion -->

## Memory System

### Dual Memory: Why Auto-Memory + External Persistence
<!-- Auto-memory is session-scoped. AI-MemoryCore survives across machines, IDE reinstalls, and account changes -->

### MEMORY.md Under 100 Lines: Why Extract to Topics
<!-- Lines after 200 are truncated. Common Gotchas section grew to 28 lines and was approaching the limit -->

### Topic Files: Why On-Demand Loading
<!-- Loading all NAS conventions into every session (including portfolio work) wasted context tokens -->
