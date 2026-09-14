---
name: project-CHANGEME
description: CHANGEME—one-liner describing what this project/effort does
metadata:
  type: project
---

# Project Name

**Repos**:
- git@gitlab.com:redhat/rhel-ai/workflow-validation/CHANGEME.git

## What This Project Does

One paragraph describing the project's purpose, scope, and your responsibilities within it.

## Related Jira

- **Epic**: https://redhat.atlassian.net/browse/AIPCC-XXXX
- **Initiative**: https://redhat.atlassian.net/browse/AIPCC-XXXX
- **Key stories**: AIPCC-XXXX, AIPCC-XXXX

## Repos & Dependencies

| Repo | Purpose | Primary work? | Notes |
|------|---------|---------------|-------|
| repo-1 | What it does | Yes/No | Dependencies, build role, etc. |
| repo-2 | What it does | Yes/No | |

If single-repo project, delete this table.

## Build Flow / Integration

Describe how repos interact (if multi-repo):
- Repo A builds image that includes Repo B and C
- Changes to Repo B require rebuilding Repo A
- etc.

If single-repo, describe the project's architecture/structure instead.

## Required Credentials

Source from `~/aiworkspace/.env` — configured in `~/.config/opencode/opencode.jsonc`:

- `VAR_NAME` — description
- `VAR_NAME` — description

Or: None (if no special credentials needed)

## Daily Workflow

1. **Check repo activity** — `git fetch && git log origin/main -10 --oneline` (per repo); `glab mr list` for MRs
2. **Understand colleague changes** — Review recent MRs and commits; what broke, what improved?
3. **[Project-specific task]** — e.g., "Verify container build succeeds after changes to dependencies"

## Key Files & Structure

- `.gitignore` — project ignores
- `Dockerfile` / `.gitlab-ci.yml` — build/deployment
- `src/` — source code
- `tests/` — test suites
- Other key directories or build artifacts

## Before Each Coding Session

1. Pull latest from all repos
2. Review unreviewed MRs
3. Understand what changed since last session (use `git log` and colleague notifications)
4. If multi-repo: verify the repos are still compatible (e.g., no breaking changes in dependencies)

## Related Memory Files

- [[related-project-name]] — if this project depends on or interacts with another tracked project
