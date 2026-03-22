# Brainstorm: Unified /go Workflow Skill

**Date:** 2026-03-21
**Status:** Ready for planning

## What We're Building

A single unified `/go` workflow skill that replaces 9 separate go-* commands with one parameterized skill. The skill is generic enough to distribute via the marketplace — no hardcoded usernames, repos, test runners, or auth setups.

### Current State (9 files)

| Command | What it does |
|---------|-------------|
| go-ham | Full workflow: plan + deepen + implement + review + browser test + push |
| go-lite | Balanced: plan + implement + review + browser test + push |
| go-lite-noweb | Fast: plan + implement + review + push |
| go-bug | Lightweight bug fix: quick RCA + single subagent + verify |
| go-ham-auto | go-ham with autonomous brainstorm prepended |
| go-lite-auto | go-lite with autonomous brainstorm prepended |
| go-lite-noweb-auto | go-lite-noweb with autonomous brainstorm prepended |
| go-bug-auto | go-bug with autonomous brainstorm prepended |
| go-bug-parallel | Dispatcher: runs go-bug on multiple issues in parallel |

### Target State (1 skill)

A single `/go` skill with a mode parameter:

```
/go ham #123 redesign auth flow
/go lite fix null pointer in user service
/go lite-noweb add config validation
/go bug #456 fix crash on empty input
/go ham-auto complex new feature that needs exploration
/go bug-parallel #674 #540 #123
```

## Why This Approach

1. **Compounding** — Fix a bug once, fixed everywhere. Add a phase once, available in all modes.
2. **Marketplace-ready** — No project-specific hardcoding. Works for any language/framework.
3. **Maintainability** — 9 files with ~85% duplication collapsed to 1. No more drift between copies.
4. **Composable** — `-auto` is a modifier on any mode, not a separate file.

## Key Decisions

### 1. Invocation Style

**Decision:** `/go <mode> [args]` with `-auto` as a suffix variant.

- `/go ham`, `/go lite`, `/go lite-noweb`, `/go bug`
- `/go ham-auto`, `/go lite-auto`, `/go bug-auto`
- `/go bug-parallel` (dispatcher mode)

### 2. Configuration: Auto-detect + Config Override

**Decision:** Smart auto-detection for common settings, with override via `compound-engineering.local.md` frontmatter.

The skill auto-detects at runtime:
- **Test command** — sniff `package.json` (npm test), `pyproject.toml` (pytest), `Makefile` (make test), `Gemfile` (bundle exec rspec), `go.mod` (go test), `Cargo.toml` (cargo test)
- **Frontend directory** — scan for common dirs (`frontend/`, `dashboard/`, `app/`, `client/`, `web/`)

Users override in `compound-engineering.local.md`:
```yaml
test_command: "npm test"
frontend_dir: "dashboard"
```

### 3. Handling the 9 Portability Issues

| # | Issue | Resolution |
|---|-------|-----------|
| 1 | Hardcoded GitHub project board | **Stripped (BYO).** Document how to add via project-level command override. Too many task tools exist to generalize. |
| 2 | Python-only test runner | **Auto-detect + config override.** Sniff from project files, override with `test_command:`. |
| 3 | Clerk auth for browser testing | **Stripped.** Auth is too project-specific. Skill just opens pages. Auth setup belongs in project CLAUDE.md. |
| 4 | `dashboard/` directory assumption | **Auto-detect + config override.** Scan for frontend dirs, override with `frontend_dir:`. |
| 5 | "tickers/data" domain language | **Rewrite.** Say "real data" instead. |
| 6 | Missing skill dependencies | **Already conditional.** Keep the "if installed, use it; otherwise skip" pattern. |
| 7 | PEP 8 conventions in prompts | **Rewrite.** Say "follow project's CLAUDE.md conventions." |
| 8 | `options CLI` reference | **Rewrite.** Drop it. |
| 9 | `bypassPermissions` | **Rewrite.** Change to `auto`. |

### 4. Phase Table (Mode = Feature Flags)

The unified skill has a master phase list. Each mode enables a subset:

| Phase | lite-noweb | lite | ham | bug |
|-------|:---:|:---:|:---:|:---:|
| Brainstorm (if -auto) | yes | yes | yes | yes |
| Worktree setup | yes | yes | yes | - |
| Planning (ce:plan) | yes | yes | yes | inline RCA |
| Deepen plan | - | - | yes | - |
| Implementation (per-task) | yes | yes | yes | single subagent |
| Review (ce:review) | yes | yes | yes | - |
| Resolve TODOs | yes | yes | yes | - |
| Simplify code (if installed) | yes | yes | yes | - |
| Compound (conditional) | yes | yes | yes | - |
| Browser testing (conditional) | - | yes | yes | conditional |
| Push | yes | yes | yes | yes |

### 5. Bug-Parallel as a Special Dispatcher Mode

`/go bug-parallel #674 #540` remains a dispatcher — it doesn't run phases itself, it spawns isolated `/go bug` agents per issue. Included in the same skill file but with completely separate logic.

### 6. BYO Task Tracking Documentation

The README and SKILL.md will document three ways to add project-specific task tracking:

1. **Project-level command override** — Create `.claude/commands/go.md` that wraps the skill
2. **CLAUDE.md pre-workflow hook** — Add instructions to project CLAUDE.md
3. **Fork** — Add Step 0.1 back with your own values

Include a commented template showing the original GitHub Projects v2 pattern as an example.

## Open Questions

None — all decisions resolved through dialogue.

## Scope

### In Scope
- Unify 9 go-* files into 1 `/go` skill
- Generalize all 9 portability issues
- Add auto-detection for test runner and frontend directory
- Add config override via `compound-engineering.local.md`
- Update README with BYO task tracking docs
- Update marketplace metadata (plugin.json, marketplace.json)

### Out of Scope
- Adding new phases or capabilities
- Changes to ce:plan, ce:review, or other referenced skills
- Building a configuration wizard/setup flow
- Supporting task tracking integrations directly
