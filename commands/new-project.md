---
description: Create a new project memory file for multi-repo or single-repo work
---

# New Project Setup

Create a new project memory file to track context for a coding effort spanning one or more GitLab/GitHub repos.

## When to Use This

- Starting work on a new epic or initiative
- Taking on a multi-repo effort (e.g., changes span multiple codebases)
- Switching focus from one project to another
- Creating a reusable reference for long-running work

## Step 1: Define the Project

Answer these questions:

1. **Project name** (for the memory file): Short kebab-case slug
   - Example: `project-workflow-validation-test-harness`
   - Used in filename: `memory/{name}.md`

2. **One-liner description**: What does this project/effort do?
   - Example: "Test harness spanning 6 interdependent repos (director, rca-agent, performer-plugin, stagecraft, ikari-ops-plugin, rhoai-customer-workflows)"

3. **Repos involved**: List all repos (single repo or multiple)
   - Example: `git@gitlab.com:redhat/rhel-ai/workflow-validation/workflow-validation-director.git`
   - List all 5-6 even if you only edit one or two

4. **Related Jira** (if any): Epic, initiative, or story URLs
   - Example: https://redhat.atlassian.net/browse/AIPCC-8304

5. **Required credentials** (if any): From `~/aiworkspace/.env` or `opencode.jsonc`
   - Example: `AWS_S3_ENDPOINT`, `PG_QA_USER`, etc.
   - Or: None (if this project doesn't need special credentials)

6. **Daily workflow**: 3-4 bullet points on what you do each session
   - Example:
     - Check MRs across all 6 repos (`glab mr list` per repo)
     - Review diff impacts (if director changed, verify dependency repos still compatible)
     - Pull latest and understand what colleagues changed

## Step 2: Copy the Template

From `~/.config/opencode/templates/project-memory-template.md`, copy and fill in:

```bash
cp ~/.config/opencode/templates/project-memory-template.md ~/.config/opencode/memory/project-{name}.md
```

Edit the new file and fill in all the sections from Step 1.

## Step 3: Update the Memory Index

Add a line to `~/.config/opencode/memory/MEMORY.md`:

```markdown
- [{project-name}](project-{name}.md) — {one-liner description}
```

## Step 4: Test

Open `opencode` in any of the repos involved. Ask:

> "Show me my project memory for {project-name}"

Or:

> "Load the context for {project-name} and remind me of today's workflow"

The memory MCP server should surface the file. If it doesn't, restart opencode.

## Example: Single-Repo Project

Project name: `project-workflow-validation-eval`
Repos: `git@gitlab.com:redhat/rhel-ai/workflow-validation/workflow-validation-eval.git`
Related Jira: https://redhat.atlassian.net/browse/AIPCC-8304
Credentials: `AWS_S3_*`, `PG_QA_*`

Result: `~/.config/opencode/memory/project-workflow-validation-eval.md`

## Example: Multi-Repo Project

Project name: `project-workflow-validation-test-harness`
Repos:
- workflow-validation-director
- workflow-validation-rca-agent
- workflow-validation-performer-plugin
- workflow-validation-stagecraft
- ikari-ops-plugin
- rhoai-customer-workflows

Related Jira: [your epic]
Credentials: [shared set]

Result: `~/.config/opencode/memory/project-workflow-validation-test-harness.md`

---

## Notes

- Memory files are local to `~/.config/opencode/memory/` and not git-tracked
- One memory file per project, regardless of how many repos it spans
- Update the memory file whenever repo relationships change or new repos are added
- If a project is archived or you're done with it, you can delete its memory file (it won't break anything)
