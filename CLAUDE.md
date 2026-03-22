@AGENTS.md

# Fork-Specific Instructions (t851029/skills-repo)

This is a fork of EveryInc/compound-engineering-plugin. These instructions supplement the upstream AGENTS.md.

## Git Remotes

- `origin` = `t851029/skills-repo` (this fork)
- `upstream` = `EveryInc/compound-engineering-plugin` (original)

## PRs Must Target This Fork

Always use `--repo t851029/skills-repo` with `gh pr create`. Without it, `gh` defaults to the upstream EveryInc repo.

## Upstream Sync

A GitHub Actions workflow (`.github/workflows/sync-upstream.yml`) runs every other day to auto-merge upstream changes. If it hits a merge conflict, it fails and requires manual resolution.

## Custom Addition: Unified /go Skill

This fork adds `plugins/compound-engineering/skills/go/SKILL.md` — a unified workflow skill supporting modes: `ham`, `lite`, `lite-noweb`, `bug`, `bug-parallel`, plus `-auto` suffix for brainstorm. This skill does not exist upstream.
