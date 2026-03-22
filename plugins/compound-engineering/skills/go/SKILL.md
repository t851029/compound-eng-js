---
name: go
description: Autonomous development workflow — plan, implement, review, and push. Modes: ham (full), lite (balanced), lite-noweb (fast), bug (lightweight fix), bug-parallel (multi-issue).
argument-hint: "<mode> [description or issue #]"
disable-model-invocation: true
---

# Go — Autonomous Development Workflow

Single entry point for all autonomous development workflows. Parses the first token of `$ARGUMENTS` to select a mode, then executes the appropriate phase pipeline end-to-end.

**Model routing**: Opus for strategic phases (plan, deepen, review, RCA) via Skill tool. Sonnet for execution phases (work, fix, resolve) via Task tool / subagents.

**MANDATORY** (standard modes): ALL phases must execute in order. Do NOT skip phases, combine phases, or decide the task is "too simple" for the workflow. The user explicitly chose this workflow — respect that choice.

## Input

<user_arguments>
$ARGUMENTS
</user_arguments>

---

## Mode Parsing

Parse the first whitespace-delimited token from `$ARGUMENTS` as `MODE`. Everything after the first token is `REMAINING_ARGS`.

**Recognized modes:**

| Mode | Description |
|------|-------------|
| `ham` | Full workflow — deepen-plan + browser testing |
| `lite` | Balanced — browser testing, no deepen |
| `lite-noweb` | Fast — no browser testing, no deepen |
| `bug` | Lightweight RCA + single subagent fix |
| `bug-parallel` | Dispatcher for multiple bug issues |

Any mode with a `-auto` suffix (e.g. `ham-auto`, `lite-auto`, `bug-auto`) adds a brainstorm phase before all other phases. Strip the suffix to get the base mode.

**Error handling:**

- **No mode provided** — Output usage help and stop:
  ```
  Usage: /go <mode> [description or issue #]

  Modes:
    ham           Full workflow (deepen-plan + browser testing)
    lite          Balanced (browser testing, no deepen)
    lite-noweb    Fast (no browser testing, no deepen)
    bug           Lightweight RCA + single fix subagent
    bug-parallel  Dispatch multiple bug issues in parallel

  Add -auto suffix to any mode for autonomous brainstorm first:
    /go ham-auto "add user notifications"
    /go bug-auto #674
  ```

- **Unrecognized mode** — Output an error and stop:
  ```
  Error: unrecognized mode "<MODE>"
  Available modes: ham, lite, lite-noweb, bug, bug-parallel
  (Add -auto suffix to any mode for brainstorm phase)
  ```

---

## Runtime Configuration

Detect project settings at startup. These values are used throughout all phases.

### Test Command Detection

Check for the following files in order, use the first match as `TEST_COMMAND`:

1. `package.json` present → `npm test`
2. `pyproject.toml` present → `python -m pytest tests/ -v`
3. `Gemfile` present → `bundle exec rspec`
4. `go.mod` present → `go test ./...`
5. `Cargo.toml` present → `cargo test`
6. `Makefile` present with a `test` target → `make test`
7. None found → warn "No test command detected — skipping test phases" and set `TEST_COMMAND` to empty

### Frontend Directory Detection

Scan for these directories (only count as frontend if the directory contains `package.json`):
`frontend/`, `dashboard/`, `client/`, `web/`, `app/`

Use the first match as `FRONTEND_DIR`. If none match, set `FRONTEND_DIR` to empty — browser testing will be skipped.

### Config Overrides

If `compound-engineering.local.md` exists in the project root, read its YAML frontmatter for:
- `test_command:` — overrides auto-detected `TEST_COMMAND`
- `frontend_dir:` — overrides auto-detected `FRONTEND_DIR`

Log the resolved values before proceeding:
```
Runtime config:
  TEST_COMMAND: <value or "none detected">
  FRONTEND_DIR: <value or "none">
```

---

## Phase Table

Which phases run in each mode:

| Phase | lite-noweb | lite | ham | bug |
|-------|:---:|:---:|:---:|:---:|
| Brainstorm (if -auto suffix) | yes | yes | yes | yes |
| Worktree setup | yes | yes | yes | — |
| Planning (ce:plan) | yes | yes | yes | inline RCA |
| Deepen plan | — | — | yes | — |
| Implementation | yes | yes | yes | single subagent |
| Review (ce:review) | yes | yes | yes | — |
| Resolve TODOs | yes | yes | yes | — |
| Simplify code (if installed) | yes | yes | yes | — |
| Compound (conditional) | yes | yes | yes | — |
| Browser testing (conditional) | — | yes | yes | conditional |
| Push | yes | yes | yes | yes |

`bug-parallel` is a pure dispatcher — it does not run phases itself. It spawns one agent per issue, each running `/go bug #NNN`.

---

## Bug-Parallel Mode

**Run this section if and only if `MODE == bug-parallel`. Skip all other sections.**

### Step 1: Parse Issues

Extract all issue numbers from `REMAINING_ARGS`. Accept any format:
- `#674 #540` or `674 540` or `#674, #540`

Strip `#` prefixes, commas, and extra whitespace. Collect as a list.

- **Zero issues found** → tell the user: "Usage: /go bug-parallel #674 #540 #123" and stop.
- **Exactly one issue** → tell the user: "Only one issue found — use `/go bug #NNN` directly instead." and stop.
- **More than 5 issues** → tell the user: "Too many issues (max 5 parallel). Batch them into groups of ≤5." and stop.

### Step 2: Dispatch Parallel Agents

For **each** issue number, spawn one Agent in a single message (all tool calls in one response):

```
Agent tool:
  description: "bug-NNN"
  model: opus
  mode: auto
  isolation: worktree
  run_in_background: true
  name: "bug-NNN"
  prompt: |
    /go bug #NNN
```

Report to the user immediately:
```
Dispatched N bug-fix agents in parallel:
- bug-NNN: [issue title or description if known]
- bug-MMM: [issue title or description if known]

You'll be notified as each completes.
```

### Step 3: Report Results

As each background agent completes (you'll be notified automatically), summarize:
- PR URL
- Fix summary (1 line)
- Pass/fail status

Do not retry failed agents — relay the failure message and let the user decide.

After ALL agents complete, output a final summary table:
```
## All Bug Fixes Complete

| Issue | PR | Status |
|-------|----|--------|
| #NNN  | #XX | Merged |
| #MMM  | #XX | Failed — escalate to /go lite |
```

---

## Bug Mode

**Run this section if and only if base `MODE == bug`. Skip all other sections.**

### Phase 0 (if AUTO_MODE): Brainstorm

Use the Skill tool to run an autonomous brainstorm focused on the bug:

```
skill: "compound-engineering:ce:brainstorm"
args: "--auto <REMAINING_ARGS>"
```

Wait for Phase 0 to complete. Note the brainstorm doc path (e.g. `docs/brainstorms/...brainstorm.md`) for use in Phase 1.

**LOW-confidence gate:** If the brainstorm output warns about low-confidence decisions, display them. The user may want to resolve these before investing in a fix. If no response within 30 seconds, proceed.

### Phase 1: Quick RCA (Opus — main thread, ~30 seconds)

**Do all of this yourself. No subagents. No skill invocations. Just think.**

1. If AUTO_MODE: Read the brainstorm doc to understand the explored problem space.

2. **Check learnings** — Search `docs/solutions/` for prior fixes matching this bug:
   ```bash
   rg -l "<relevant keywords>" docs/solutions/
   ```
   Read the top 1-2 matches if found.

3. **Read relevant code** — Identify and read the 2-3 files most likely involved. Use Grep/Read, not an agent.

4. **Write the plan** — Produce a concise inline plan (NOT a file, NOT a subagent). Format:

   ```
   ## Bug Fix Plan

   **Root cause:** [1-2 sentences]
   **Prior fix found:** [yes/no — reference if yes]
   **Brainstorm insights:** [key findings if AUTO_MODE, otherwise omit]
   **Files to change:** [list]
   **Fix approach:** [2-5 bullet points, specific enough for someone with zero context]
   **Test:** [what test to write, what it should assert]
   **Verify:** [one CLI command that proves the fix works with real data, if applicable]
   ```

5. **Parse issue number** — If `REMAINING_ARGS` contains `#NNN`, store as `ISSUE_NUMBER`.

**Time budget: 30 seconds of thinking. If still researching after 2 minutes, STOP. Tell the user: "This bug is too complex for /go bug — use /go lite instead."**

### Phase 2: Execute (Sonnet — single subagent)

Dispatch ONE subagent to implement the fix:

```
Task tool:
  subagent_type: general-purpose
  model: sonnet
  description: "Fix: [short bug description]"
  prompt: |
    You are fixing a bug. Here is the complete plan — implement it exactly.

    [PASTE THE PLAN FROM PHASE 1 HERE]

    ## Instructions
    1. Read the files listed in "Files to change"
    2. Write a failing test FIRST (the test described in the plan)
    3. Run the test, confirm it fails
    4. Implement the fix
    5. Run the test, confirm it passes
    6. Run the full test suite: <TEST_COMMAND>
    7. Commit with message: "fix: [description]"

    Report back: what you changed, test results, any concerns.
```

**Critical:** The subagent gets ONLY the plan text (~500 bytes). Not the full conversation. Not the file contents already read. Just the plan. This prevents scope creep.

Wait for the subagent to complete. If it reports BLOCKED, handle it yourself or tell the user.

### Phase 3: Verify + Ship

1. **Run test suite** — If the subagent did not already run it:
   ```bash
   <TEST_COMMAND> 2>&1 | tail -20
   ```

2. **Real data validation** — Run the fix against real data (not mocks, not fixtures):
   - If the plan has a "Verify" command, run it now
   - Confirm output is correct
   - **Hard gate**: Do NOT proceed to shipping until real-data validation passes

3. **Frontend check** (conditional — only if `git diff --name-only` shows `FRONTEND_DIR` files):
   ```bash
   cd <FRONTEND_DIR> && npm run build
   ```
   - **Build fails** → STOP. The subagent broke something. Fix before shipping.
   - **Build passes + dev server running** → Use agent-browser to open the affected route, take a snapshot and screenshot. If the project requires auth, follow the setup described in the project's CLAUDE.md.
   - **Build passes + no dev server** → Skip screenshot. Build pass is sufficient.

   If no `FRONTEND_DIR` files changed, skip entirely.

4. **Push** — If the `push` skill is installed:
   ```
   skill: "push"
   ```
   Otherwise:
   ```bash
   git add -A
   git commit -m "fix: <description>"
   git push -u origin $(git branch --show-current)
   gh pr create --title "fix: <description>" --body "## Summary\n\n<fix description>\n\nCloses #ISSUE_NUMBER"
   ```

5. **Report:**
   ```
   ## Go Bug Complete

   **PR:** [url]
   **Fix:** [1 sentence]
   **Tests:** [pass/fail count]
   ```

### Bug Mode Guardrails

- **Max 3 files changed.** If the plan needs more, STOP — use `/go lite`.
- **Max 1 subagent.** No swarms, no parallel agents, no reviewers. One shot.
- **No plan file.** The plan lives in the conversation, not on disk. Bugs don't need documentation.
- **No research subagents.** Read `docs/solutions/` directly via grep. No learnings-researcher dispatch.
- **2-minute planning cap.** If RCA takes longer, the bug is too complex. Escalate to `/go lite`.
- **Solution doc:** Skip for bug mode. If the fix is non-trivial enough to document, use `/go lite` instead.

---

## Standard Mode Phases (ham, lite, lite-noweb)

**Run this section if and only if base `MODE` is `ham`, `lite`, or `lite-noweb`.**

**MANDATORY**: ALL phases must execute in order. Do NOT skip phases, combine phases, or decide the task is "too simple" for the workflow.

---

### Phase 0 (if AUTO_MODE): Brainstorm

Echo: "Phase 0: Brainstorm"

Use the Skill tool:

```
skill: "compound-engineering:ce:brainstorm"
args: "--auto <REMAINING_ARGS>"
```

Wait for Phase 0 to complete. Note the brainstorm doc path for Phase 1.

---

### Phase 0.5: Worktree Setup

Echo: "Phase 0.5: Worktree Setup"

1. Derive a branch name from `REMAINING_ARGS`:
   - Slugify: lowercase, spaces → hyphens, strip special chars, max 50 chars
   - Prefix: `feat/`, `fix/`, or `chore/` based on request intent
   - Example: "add volume alerts" → `feat/add-volume-alerts`

2. Create the worktree:
   ```bash
   bash ${CLAUDE_PLUGIN_ROOT}/skills/git-worktree/scripts/worktree-manager.sh create <branch-name>
   ```

3. `cd` into the new worktree:
   ```bash
   cd .worktrees/<branch-name>
   ```

4. Confirm you are on the new branch before proceeding.

---

### Phase 1: Planning (Opus — Skill tool)

Echo: "Phase 1: Planning"

Use the Skill tool:

```
skill: "compound-engineering:ce:plan"
args: "<brainstorm doc path if AUTO_MODE, otherwise REMAINING_ARGS>"
```

Wait for Phase 1 to complete. Note the plan file path (e.g. `docs/plans/...plan.md`) for subsequent phases.

---

### Phase 2 (ham only): Deepen Plan (Opus — Skill tool)

Echo: "Phase 2: Deepen Plan"

**Skip this phase if MODE is `lite` or `lite-noweb`.**

Use the Skill tool:

```
skill: "compound-engineering:deepen-plan"
args: "<plan file path from Phase 1>"
```

Wait for Phase 2 to complete before proceeding.

---

### Phase 3: Implementation (Sonnet — work-executor agent)

Echo: "Phase 3: Implementation"

Use the Task tool to spawn the work-executor agent:

```
Task tool:
  subagent_type: work-executor
  description: "Implement plan"
  prompt: |
    Plan file: <plan file path from Phase 1>

    Execute the plan completely using the work workflow in your context.
    The user's original request: <REMAINING_ARGS>
```

Wait for Phase 3 to complete before proceeding.

---

### Phase 4: Review (Opus — Skill tool)

Echo: "Phase 4: Review"

Use the Skill tool:

```
skill: "compound-engineering:ce:review"
args: "latest"
```

Wait for Phase 4 to complete before proceeding.

---

### Phase 5: Resolve TODOs (Sonnet — Task tool)

Echo: "Phase 5: Resolve TODOs"

Use the Task tool to spawn a Sonnet subagent:

```
Task tool:
  subagent_type: general-purpose
  model: sonnet
  description: "Fix review findings"
  prompt: |
    Fix all review findings from the todos/ directory.

    Instructions:
    1. List all pending todo files: ls todos/*-pending-*.md
    2. For each todo file, in priority order (p1 first, then p2, then p3):
       - Read the todo file
       - Understand the problem and proposed solution
       - Implement the fix
       - Run relevant tests to verify
       - Rename the file from pending to complete
         (e.g. 001-pending-p1-foo.md → 001-complete-p1-foo.md)
    3. After all fixes: run full test suite using: <TEST_COMMAND>
    4. Report what was fixed and any remaining issues
```

Wait for Phase 5 to complete before proceeding.

---

### Phase 6: Simplify Code (Conditional)

Echo: "Phase 6: Simplify Code"

If the `simplifycode` skill is installed, invoke it:

```
skill: "simplifycode"
```

If not installed, skip this phase and note it in the summary.

Wait for Phase 6 to complete before proceeding.

---

### Phase 7: Compound (Conditional)

Echo: "Phase 7: Compound"

If this workflow resolved a bug, integration issue, or non-obvious problem, document the solution:

```
skill: "compound-engineering:ce:compound"
```

If the work was straightforward feature implementation with no novel solutions, skip this phase and note it in the summary.

Wait for Phase 7 to complete before proceeding.

---

### Phase 8 (lite and ham only): Browser Testing (Conditional)

Echo: "Phase 8: Browser Testing"

**Skip this phase if MODE is `lite-noweb`.**

**Skip this phase if `FRONTEND_DIR` is empty.**

**Skip this phase if `git diff --name-only` shows no changes under `FRONTEND_DIR`.**

If running, use the Task tool:

```
Task tool:
  subagent_type: general-purpose
  model: sonnet
  description: "Browser testing"
  prompt: |
    Run browser tests on pages affected by recent changes.

    Instructions:
    1. Check git diff to identify changed files under <FRONTEND_DIR>
    2. Determine which pages/routes are affected
    3. Ensure the dev server is running (cd <FRONTEND_DIR> && npm run dev)
    4. For each affected page:
       - agent-browser open <local-url>/<route>
       - agent-browser snapshot -i  (check interactive elements render)
       - agent-browser screenshot <route-name>.png
    5. Report any issues found (missing elements, errors in snapshot)
    6. agent-browser close

    If the project requires authentication, follow the setup described in the
    project's CLAUDE.md. Do not hardcode credentials or auth-specific flows.
```

Wait for Phase 8 to complete before proceeding.

---

### Phase 9: Push

Echo: "Phase 9: Push"

If the `push` skill is installed:

```
skill: "push"
```

Otherwise:

```bash
git add -A
git commit -m "feat: <description based on work done>"
git push -u origin $(git branch --show-current)
gh pr create --title "<title>" --body "## Summary

<description>

## Changes

<list of changes>"
```

---

## BYO Task Tracking

The `/go` skill intentionally omits project-specific task tracking (GitHub Projects, Jira, Linear, etc.) to remain generic. Add task tracking via one of these approaches:

**Option 1: Project-level command override**
Create `.claude/commands/go.md` in your project. Claude Code will use your local override instead of this skill. Copy the relevant mode section and add your tracking steps.

**Option 2: CLAUDE.md pre-workflow instructions**
Add a section to your project's `CLAUDE.md` that instructs Claude to run tracking steps before invoking `/go`. Claude reads CLAUDE.md at the start of every session.

**Option 3: Fork the plugin**
Fork the compound-engineering plugin and edit this SKILL.md directly to add your tracking logic permanently.

<!-- Example: GitHub Projects v2
ISSUE_NUMBER=$(echo "$ARGUMENTS" | grep -oE '#[0-9]+' | head -1 | tr -d '#')
PROJECT_ID=$(gh project view <NUMBER> --owner <OWNER> --format json --jq '.id')
ITEM_ID=$(gh project item-list <NUMBER> --owner <OWNER> --limit 200 --format json \
  | python3 -c "import json,sys; data=json.load(sys.stdin); matches=[i for i in data['items'] if i.get('content',{}).get('number')==$ISSUE_NUMBER]; print(matches[0]['id'] if matches else '')")
field_updater.sh set-status "$PROJECT_ID" "$ITEM_ID" "Implementation" || true
-->

---

## Enforcement Rules

### Rule 1: No Phase Skipping (standard modes only)

Forbidden rationalizations — never accept these as reasons to skip a phase:

- "The task is too simple for planning"
- "The implementation is obvious, review would be redundant"
- "There are no TODOs to resolve"
- "The changes don't touch the frontend so browser testing can be skipped entirely" *(it can only be skipped when FRONTEND_DIR is empty or no files changed)*
- "The user just wants a quick fix"
- "I already know the answer"

The only valid reasons to skip a phase are the explicit conditional rules stated above (mode doesn't include phase, FRONTEND_DIR empty, no frontend files changed).

### Rule 2: No Substitutions

Do not substitute the specified tool invocations with alternatives:
- Use the `skill:` tool invocations as written — not direct Bash equivalents
- Use the Task tool for subagents — not inline tool calls in the main thread
- Use `ce:review` for review — not a manual grep-and-comment loop

### Rule 3: Echo Before Execute

Before each phase, output the echo line (e.g. "Phase 3: Implementation") so the user can track progress. This is not optional.

---

## Completion Summary

After all phases complete, output a summary. Only list phases that actually ran for the selected mode.

**Standard modes (ham / lite / lite-noweb):**
```
## Go <MODE> Complete

- Phase 0 (Brainstorm): [ran / skipped — not -auto mode]
- Phase 0.5 (Worktree): Branch <branch-name> created
- Phase 1 (Planning): Plan at <plan file path>
- Phase 2 (Deepen): [ran / skipped — not ham mode]
- Phase 3 (Implementation): [what was built]
- Phase 4 (Review): [N issues found]
- Phase 5 (Resolve TODOs): [N fixed, N remaining]
- Phase 6 (Simplify): [ran / skipped — not installed]
- Phase 7 (Compound): [ran / skipped — straightforward work]
- Phase 8 (Browser testing): [ran / skipped — reason]
- Phase 9 (Push): PR opened at <url>

Files created/modified: [list]
```

**Bug mode:**
```
## Go Bug Complete

- Phase 0 (Brainstorm): [ran / skipped — not -auto mode]
- Phase 1 (RCA): Root cause identified
- Phase 2 (Execute): Fix implemented by Sonnet subagent
- Phase 3 (Verify + Ship): PR opened at <url>

Tests: [pass/fail count]
```

**Bug-parallel mode:**

See the summary table in the Bug-Parallel Mode section above.

<promise>DONE</promise>
