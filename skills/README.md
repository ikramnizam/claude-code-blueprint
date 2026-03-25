# Skills

17 domain-specific skills triggered by natural language (no slash commands needed).

## Skill Categories

| Category | Skills | Triggers |
|----------|--------|----------|
| **Code Quality** | review, review-diff | "is this secure?", "scan diff", "check for vulnerabilities" |
| **Testing** | test-check, e2e-check | "run the tests", "browser test", "are tests passing?" |
| **Deployment** | deploy-check | "deploy", "push to prod", "ready to ship", "npm audit" |
| **Planning** | sprint-plan, elicit-requirements | "let's build", "new feature", multi-step tasks |
| **Session** | load-session, save-session, session-end, save-diary | Session start/end, "save", "bye", "done" |
| **Project** | init-project, register-project, status, changelog | "new project", "register project", "status" |
| **Database** | db-check | "check the schema", "database health", "validate models" |
| **Utilities** | tech-radar | "what's new?", "any updates?", "should we upgrade?" |

## Design Principles

1. **Natural language triggers** — Skills detect intent from conversation, not slash commands
2. **Step-by-step workflows** — Each skill has numbered steps that Claude follows mechanically
3. **GO/NO-GO verdicts** — Review and deploy skills end with clear pass/fail decisions
4. **Multi-agent orchestration** — The review skill spawns code-reviewer + security-reviewer + db-analyst in parallel

## Customization

Most skills reference `CLAUDE.md` and `{MEMORYCORE_PATH}` as configuration sources. Replace these tokens with your actual paths when adopting.
