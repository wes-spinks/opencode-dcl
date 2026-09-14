---
description: Review GitLab CI artifacts using Hakai adversarial analysis
agent: build
---

# Review GitLab CI Artifacts

You are performing an adversarial review of GitLab CI job artifacts using the Hakai methodology. Your job is to fetch artifacts from a GitLab CI job, optionally compare them against test specs and reference documentation, and produce a rigorous three-pass analysis.

## Input

Parse `$ARGUMENTS` to extract:

1. **Job URL** (required) — a GitLab CI job or artifacts URL, e.g.:
   - `https://<gitlab-host>/group/project/-/jobs/12345/artifacts/browse`
   - `https://gitlab.com/group/project/-/jobs/12345`
2. **Specs source** (optional) — a GitLab/GitHub repo URL or branch path pointing to test specs, e.g.:
   - `https://<gitlab-host>/group/project/-/tree/branch/specs/`
   - `git@github.com:org/repo.git` with an optional branch/path suffix
3. **Reference source** (optional) — a repo URL or local path to authoritative reference documentation

If only a job URL is provided, ask the user:
- "Do you have test specs or reference documentation to cross-reference against these artifacts? Provide URLs/paths, or say 'none' to review artifacts standalone."

## Authentication

Detect the GitLab instance from each URL and select the correct token:

| Host | Env Var | API Base |
|------|---------|----------|
| Self-hosted / internal GitLab | `GITLABCEE_TOKEN` | `https://<host>/api/v4` |
| `gitlab.com` | `GITLAB_TOKEN` | `https://gitlab.com/api/v4` |
| `github.com` | `GITHUB_TOKEN` | `https://api.github.com` |

To determine which token to use: if the job URL host is NOT `gitlab.com`, use `GITLABCEE_TOKEN`. Otherwise use `GITLAB_TOKEN`.

Source the `.env` file first if available:
```bash
if [ -f .env ]; then set -a && source .env && set +a; fi
```

If a required token is missing, tell the user which env var to set. Do not block the entire review if only one platform's token is missing — proceed with what's available.

## Procedure

### Phase 1: Artifact Retrieval

1. Parse the job URL to extract the **project path** and **job ID**.
   - From URL pattern: `https://<host>/<group>/<project>/-/jobs/<job_id>/...`
   - URL-encode the project path for API calls (e.g., `group%2Fproject`)

2. Download job artifacts using the GitLab API:
   ```bash
   curl -s --header "PRIVATE-TOKEN: $TOKEN" \
     "$API_BASE/projects/$PROJECT_ID/jobs/$JOB_ID/artifacts" \
     -o /tmp/artifacts-$JOB_ID.zip
   ```

3. Extract artifacts to a working directory:
   ```bash
   mkdir -p /tmp/gitlab-review-$JOB_ID
   unzip -o /tmp/artifacts-$JOB_ID.zip -d /tmp/gitlab-review-$JOB_ID
   ```

4. List and catalogue the extracted artifacts. Map the directory structure and identify artifact types (test reports, logs, screenshots, coverage data, etc.).

### Phase 2: Source Collection

If specs or reference sources were provided:

1. **GitLab repo/branch**: Clone or fetch the specific branch/directory using git or the GitLab API with the appropriate token.
2. **GitHub repo**: Clone using GITHUB_TOKEN for authentication:
   ```bash
   git clone https://$GITHUB_TOKEN@github.com/org/repo.git /tmp/reference-repo
   ```
3. **Local path**: Read directly from the filesystem.

### Phase 3: Load Hakai Skills

Read the three Hakai skill files from the `_hakai/` submodule (relative to the opencode config directory):

1. `_hakai/skills/inversion-engine/SKILL.md` — Three-pass methodology
2. `_hakai/skills/ingestion/SKILL.md` — Input classification protocol
3. `_hakai/skills/report-schema/SKILL.md` — Report structure and severity taxonomy

If the `_hakai/` directory is missing, inform the user:
> "The Hakai submodule is not initialized. Run `git submodule update --init` in your opencode config directory."

### Phase 4: Determine Review Mode

Based on what was collected:

| Inputs Available | Mode | What Hakai Does |
|-----------------|------|-----------------|
| Artifacts only | Single-target | Three-pass review of artifacts alone |
| Artifacts + specs | Two-way cross-reference | Alignment check: do artifacts reflect what specs define? |
| Artifacts + specs + reference | Three-way traceability | Full chain: reference doc → specs → artifacts. Coverage and traceability scores. |

### Phase 5: Execute Hakai Three-Pass Review

Follow the Hakai execution protocol from `_hakai/agents/hakai.md`:

**Pass 1 — Erase Assumptions**: Extract every implicit assumption across five categories (STATE, TRUST, ORDERING, COMPLETENESS, ENVIRONMENTAL). Apply to all collected targets.

**Pass 2 — Stress Test & Exploit**: Generate adversarial scenarios for each assumption using the nine scenario templates (Null/Absent, Malformed, Hostile, Stale, Concurrent, Partial, Reversed, Exhausted, Replayed). Rank as CRITICAL, HIGH, or MEDIUM only.

**Pass 3 — Hardened Synthesis**: Consolidate findings. If cross-reference mode is active, build the alignment matrix mapping each requirement/spec to its artifact evidence. Status each: PRESENT / MISSING / DIVERGENT / PHANTOM. Calculate coverage and traceability scores.

Announce completion of each pass before proceeding to the next.

### Phase 6: Report

Write the report to `./hakai-review-<job_id>.md` in the current working directory (or a user-specified output path if provided in `$ARGUMENTS`).

Follow the Hakai report-schema structure:
1. Header (target, date, mode, inputs)
2. Verdict (most critical flaw, 1-3 sentences)
3. Finding count summary
4. Pass 1 assumption table
5. Pass 2 failure mode table
6. Pass 3 findings by severity
7. Cross-reference matrix (if applicable)
8. Coverage gaps (if applicable)
9. Ingestion log

After writing the report, state:
1. Report path
2. Finding count: [C] CRITICAL, [H] HIGH, [M] MEDIUM
3. Coverage/traceability scores (if cross-reference mode)
4. Verdict

Nothing else.
