---
name: ds-work-update
description: Append a timestamped progress snapshot to today's session report. Does NOT commit, hibernate, or stop work. Reads product/how-to/ds-work-update.md for project-specific additions.
---

Append a timestamped progress snapshot to today's session report. Mid-session checkpoint only — does not commit, push, or hibernate.

The target directory is: `$ARGUMENTS` (use `product/` if empty or not provided).

## Steps

1. Read `<dir>/now.md` and `<dir>/milestones.md` to understand current state.

2. Run `git log --oneline -6` and `git status --short` to see what has changed since last commit.

3. Check `<dir>/how-to/ds-work-update.md` if it exists — read it now and incorporate any project-specific state-gathering steps before writing the report (e.g. checking cluster state, reading infra status files).

4. Determine today's report file path: `<dir>/reports/YYYY-MM-DD.md` (today's date).

5. If the file does not exist, create it with this header:
```markdown
# Session Report — YYYY-MM-DD
```

6. Append a new timestamped section:
```markdown
## Update — HH:MM UTC

**Active task**: <first [~] or [ ] task in milestones.md>

### Completed since last update
- <bullet per completed item>

### In progress
- <what is currently running or being worked on>

### Key results (if any)
| Metric | Value |
|--------|-------|

### Notes / decisions
- <bugs found, config changes, observations>

---
```

7. Confirm to the user: "Updated `<dir>/reports/YYYY-MM-DD.md` with a progress snapshot at HH:MM UTC."

Do NOT commit, push, or run any shutdown steps.
