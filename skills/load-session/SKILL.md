---
name: load-session
description: "Restore session context at the start of every new conversation. Auto-triggers on session start, or when user says 'continue', 'what were we doing', 'where did we leave off'."
user-invocable: true
---

Restore context from the memory system:

1. Read `{MEMORYCORE_PATH}/core/session.md` for where we left off
2. Read `{MEMORYCORE_PATH}/core/preferences.md` for user preferences
3. Read `{MEMORY_MD_PATH}` for technical context and project conventions
4. Read `{MEMORYCORE_PATH}/core/reminders.md` for active reminders
5. Read `{MEMORYCORE_PATH}/core/decisions.md` for architectural decision context
6. Check `{MEMORYCORE_PATH}/diary/current/` for the most recent diary entry (if any)
7. Check `{MEMORYCORE_PATH}/projects/active/` for active project files (scan directory)
8. **Read relevant topic files** based on project context:
   - Check CLAUDE.md for topic file references relevant to the current project
   - Only load topic files related to the current working directory

9. **Summarize** in a concise format:
   - **Reminders**: Display open reminders prominently at the top with any deadlines highlighted
   - **Last session**: What we were working on and where we left off
   - **Pending**: What's still in progress or needs attention
   - **Active projects**: Current project(s) and their session context
   - **Priorities**: Current goals and focus areas
   - **Blockers**: Any open questions or issues from last session
   - **Preferences**: Key communication and work style preferences

Keep the summary brief and actionable — focus on what's needed to continue working effectively.
