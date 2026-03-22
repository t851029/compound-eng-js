# Changelog

## [2.49.0](https://github.com/t851029/skills-repo/compare/cli-v2.48.0...cli-v2.49.0) (2026-03-22)


### Features

* add ce:plan-beta and deepen-plan-beta skills ([04f00e7](https://github.com/t851029/skills-repo/commit/04f00e7632d0b17a2b32b1059f08f630aee145b5))
* add claude-permissions-optimizer skill ([#298](https://github.com/t851029/skills-repo/issues/298)) ([eaaba19](https://github.com/t851029/skills-repo/commit/eaaba1928bcfa00ec85468df2a07effead45159b))
* add execution mode toggle and context pressure bounds to parallel skills ([#336](https://github.com/t851029/skills-repo/issues/336)) ([216d6df](https://github.com/t851029/skills-repo/commit/216d6dfb2c9320c3354f8c9f30e831fca74865cd))
* add optional high-level technical design to plan-beta skills ([#322](https://github.com/t851029/skills-repo/issues/322)) ([3ba4935](https://github.com/t851029/skills-repo/commit/3ba4935926b05586da488119f215057164d97489))
* add unified /go workflow skill ([3a035c3](https://github.com/t851029/skills-repo/commit/3a035c389fe258dbb8fd9b190da51941dda40ea0))
* edit resolve_todos_parallel skill for complete todo lifecycle ([#292](https://github.com/t851029/skills-repo/issues/292)) ([88c89bc](https://github.com/t851029/skills-repo/commit/88c89bc204c928d2f36e2d1f117d16c998ecd096))
* fix skill transformation pipeline across all targets ([#334](https://github.com/t851029/skills-repo/issues/334)) ([4087e1d](https://github.com/t851029/skills-repo/commit/4087e1df82138f462a64542831224e2718afafa7))
* **git-worktree:** auto-trust mise and direnv configs in new worktrees ([#312](https://github.com/t851029/skills-repo/issues/312)) ([cfbfb67](https://github.com/t851029/skills-repo/commit/cfbfb6710a846419cc07ad17d9dbb5b5a065801c))
* improve `repo-research-analyst` by adding a structured technology scan ([#327](https://github.com/t851029/skills-repo/issues/327)) ([1c28d03](https://github.com/t851029/skills-repo/commit/1c28d0321401ad50a51989f5e6293d773ac1a477))
* improve reproduce-bug skill, sync agent-browser, clean up redundant skills ([#333](https://github.com/t851029/skills-repo/issues/333)) ([affba1a](https://github.com/t851029/skills-repo/commit/affba1a6a0d9320b529d429ad06fd5a3b5200bd8))
* integrate claude code auto memory as supplementary data source for ce:compound and ce:compound-refresh ([#311](https://github.com/t851029/skills-repo/issues/311)) ([5c1452d](https://github.com/t851029/skills-repo/commit/5c1452d4cc80b623754dd6fe09c2e5b6ae86e72e))
* make skills platform-agnostic across coding agents ([#330](https://github.com/t851029/skills-repo/issues/330)) ([52df90a](https://github.com/t851029/skills-repo/commit/52df90a16688ee023bbdb203969adcc45d7d2ba2))
* migrate repo releases to manual release-please ([#293](https://github.com/t851029/skills-repo/issues/293)) ([f47f829](https://github.com/t851029/skills-repo/commit/f47f829d81bbf98b8d60fc2d2d9ac5f46fdabbe5))
* **plugin:** add execution posture signaling to ce:plan-beta and ce:work ([#309](https://github.com/t851029/skills-repo/issues/309)) ([748f72a](https://github.com/t851029/skills-repo/commit/748f72a57f713893af03a4d8ed69c2311f492dbd))
* unify go-ham/lite/noweb into single /go skill ([2c4b9c7](https://github.com/t851029/skills-repo/commit/2c4b9c722818a5275a61aca39f49b3876a0bd910))


### Bug Fixes

* add cursor-marketplace as release-please component ([#315](https://github.com/t851029/skills-repo/issues/315)) ([838aeb7](https://github.com/t851029/skills-repo/commit/838aeb79d069b57a80d15ff61d83913919b81aef))
* add disable-model-invocation to beta skills and refine descriptions ([72d4b0d](https://github.com/t851029/skills-repo/commit/72d4b0dfd231d48f63bdf222b07d37ecc5456004))
* **ci:** add npm registry auth to release publish job ([#319](https://github.com/t851029/skills-repo/issues/319)) ([3361a38](https://github.com/t851029/skills-repo/commit/3361a38108991237de51050283e781be847c6bd3))
* enable release-please labeling so it can find its own PRs ([a7d6e3f](https://github.com/t851029/skills-repo/commit/a7d6e3fbba862d4e8b4e1a0510f0776e9e274b89))
* enforce release metadata consistency ([#297](https://github.com/t851029/skills-repo/issues/297)) ([51f906c](https://github.com/t851029/skills-repo/commit/51f906c9ffb94a8487bb6418549be93648b32d4a))
* gitignore .context/ directory for Conductor ([#331](https://github.com/t851029/skills-repo/issues/331)) ([0f6448d](https://github.com/t851029/skills-repo/commit/0f6448d81cbc47e66004b4ecb8fb835f75aeffe2))
* make GitHub releases canonical for release-please ([#295](https://github.com/t851029/skills-repo/issues/295)) ([78971c9](https://github.com/t851029/skills-repo/commit/78971c902771f53fcacaae09b7630e2a07e417fb))
* prevent stale release PR body by closing before regeneration ([9de830a](https://github.com/t851029/skills-repo/commit/9de830aa5b5458b7936aff54909fe8fddf475831))
* re-enable changelogs so release PRs accumulate correctly ([516bcc1](https://github.com/t851029/skills-repo/commit/516bcc1dc4bf4e4756ae08775806494f5b43968a))
* reduce release-please search depth from 500 to 50 ([f1713b9](https://github.com/t851029/skills-repo/commit/f1713b9dcd0deddc2485e8cf0594266232bf0019))
* remove close-stale-PR step that broke release creation ([178d6ec](https://github.com/t851029/skills-repo/commit/178d6ec282512eaee71ab66d45832d22d75353ec))
* remove plugin versions from marketplace.json and fix brittle test ([4952007](https://github.com/t851029/skills-repo/commit/4952007cab4e3394ebe0bb23996c8a249d9bae2e))
* review findings - setup.sh cleanup, README accuracy ([fbd6583](https://github.com/t851029/skills-repo/commit/fbd6583258317d134430512df00c2fdf0d125435))
* review fixes — stale refs, skill counts, and validation guidance ([a83e11e](https://github.com/t851029/skills-repo/commit/a83e11e982e1b5b0b264b6ab63bc74e3a50f7c28))
* **skills:** update ralph-wiggum references to ralph-loop in lfg/slfg ([#324](https://github.com/t851029/skills-repo/issues/324)) ([ac756a2](https://github.com/t851029/skills-repo/commit/ac756a267c5e3d5e4ceb2f99939dbb93491ac4d2))
* skip validate and stale-close on release PR merges ([6af241e](https://github.com/t851029/skills-repo/commit/6af241e9b5ce74a173e2c32c77944a42b6d9c4fd))
* stabilize compound-engineering component counts ([#299](https://github.com/t851029/skills-repo/issues/299)) ([754c2a8](https://github.com/t851029/skills-repo/commit/754c2a893bd8a7381b5e498e935059efd86031a3))

## [2.48.0](https://github.com/EveryInc/compound-engineering-plugin/compare/cli-v2.47.0...cli-v2.48.0) (2026-03-22)


### Features

* **git-worktree:** auto-trust mise and direnv configs in new worktrees ([#312](https://github.com/EveryInc/compound-engineering-plugin/issues/312)) ([cfbfb67](https://github.com/EveryInc/compound-engineering-plugin/commit/cfbfb6710a846419cc07ad17d9dbb5b5a065801c))
* make skills platform-agnostic across coding agents ([#330](https://github.com/EveryInc/compound-engineering-plugin/issues/330)) ([52df90a](https://github.com/EveryInc/compound-engineering-plugin/commit/52df90a16688ee023bbdb203969adcc45d7d2ba2))

## [2.47.0](https://github.com/EveryInc/compound-engineering-plugin/compare/cli-v2.46.0...cli-v2.47.0) (2026-03-20)


### Features

* improve `repo-research-analyst` by adding a structured technology scan ([#327](https://github.com/EveryInc/compound-engineering-plugin/issues/327)) ([1c28d03](https://github.com/EveryInc/compound-engineering-plugin/commit/1c28d0321401ad50a51989f5e6293d773ac1a477))


### Bug Fixes

* **skills:** update ralph-wiggum references to ralph-loop in lfg/slfg ([#324](https://github.com/EveryInc/compound-engineering-plugin/issues/324)) ([ac756a2](https://github.com/EveryInc/compound-engineering-plugin/commit/ac756a267c5e3d5e4ceb2f99939dbb93491ac4d2))

## [2.46.0](https://github.com/EveryInc/compound-engineering-plugin/compare/cli-v2.45.0...cli-v2.46.0) (2026-03-20)


### Features

* add optional high-level technical design to plan-beta skills ([#322](https://github.com/EveryInc/compound-engineering-plugin/issues/322)) ([3ba4935](https://github.com/EveryInc/compound-engineering-plugin/commit/3ba4935926b05586da488119f215057164d97489))


### Bug Fixes

* **ci:** add npm registry auth to release publish job ([#319](https://github.com/EveryInc/compound-engineering-plugin/issues/319)) ([3361a38](https://github.com/EveryInc/compound-engineering-plugin/commit/3361a38108991237de51050283e781be847c6bd3))

## [2.45.0](https://github.com/EveryInc/compound-engineering-plugin/compare/cli-v2.44.0...cli-v2.45.0) (2026-03-19)


### Features

* edit resolve_todos_parallel skill for complete todo lifecycle ([#292](https://github.com/EveryInc/compound-engineering-plugin/issues/292)) ([88c89bc](https://github.com/EveryInc/compound-engineering-plugin/commit/88c89bc204c928d2f36e2d1f117d16c998ecd096))
* integrate claude code auto memory as supplementary data source for ce:compound and ce:compound-refresh ([#311](https://github.com/EveryInc/compound-engineering-plugin/issues/311)) ([5c1452d](https://github.com/EveryInc/compound-engineering-plugin/commit/5c1452d4cc80b623754dd6fe09c2e5b6ae86e72e))


### Bug Fixes

* add cursor-marketplace as release-please component ([#315](https://github.com/EveryInc/compound-engineering-plugin/issues/315)) ([838aeb7](https://github.com/EveryInc/compound-engineering-plugin/commit/838aeb79d069b57a80d15ff61d83913919b81aef))

## [2.44.0](https://github.com/EveryInc/compound-engineering-plugin/compare/cli-v2.43.2...cli-v2.44.0) (2026-03-18)


### Features

* **plugin:** add execution posture signaling to ce:plan-beta and ce:work ([#309](https://github.com/EveryInc/compound-engineering-plugin/issues/309)) ([748f72a](https://github.com/EveryInc/compound-engineering-plugin/commit/748f72a57f713893af03a4d8ed69c2311f492dbd))

## [2.43.2](https://github.com/EveryInc/compound-engineering-plugin/compare/cli-v2.43.1...cli-v2.43.2) (2026-03-18)


### Bug Fixes

* enable release-please labeling so it can find its own PRs ([a7d6e3f](https://github.com/EveryInc/compound-engineering-plugin/commit/a7d6e3fbba862d4e8b4e1a0510f0776e9e274b89))
* re-enable changelogs so release PRs accumulate correctly ([516bcc1](https://github.com/EveryInc/compound-engineering-plugin/commit/516bcc1dc4bf4e4756ae08775806494f5b43968a))
* reduce release-please search depth from 500 to 50 ([f1713b9](https://github.com/EveryInc/compound-engineering-plugin/commit/f1713b9dcd0deddc2485e8cf0594266232bf0019))
* remove close-stale-PR step that broke release creation ([178d6ec](https://github.com/EveryInc/compound-engineering-plugin/commit/178d6ec282512eaee71ab66d45832d22d75353ec))

## Changelog

Release notes now live in GitHub Releases for this repository:

https://github.com/EveryInc/compound-engineering-plugin/releases

Multi-component releases are published under component-specific tags such as:

- `cli-vX.Y.Z`
- `compound-engineering-vX.Y.Z`
- `coding-tutor-vX.Y.Z`
- `marketplace-vX.Y.Z`

Do not add new release entries here. New release notes are managed by release automation in GitHub.
