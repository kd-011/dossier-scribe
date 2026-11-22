---
slug: dossier-scribe-agents
name: dossier-scribe agent guide
repo: .
status: active
last_verified: 2026-11-22
---

# dossier-scribe: guide for coding agents

The doc index is docs/README.md. Read docs/ARCHITECTURE.md before changing structure. What the tool is for and how to use it is in README.md.

## Commands

- Run every test with `npm test`, which is `node --test`. <!-- src: package.json#scripts.test -->
- Run one test file while iterating: `node --test test/pin-check.test.mjs`. <!-- src: CONTRIBUTING.md:15-17 -->
- Run the CLI from a checkout with `node bin/dossier-scribe.mjs <command>`. The installed names are `dossier-scribe` and `dscribe`. <!-- src: package.json#bin, bin/dossier-scribe.mjs:1-4 -->
- See it work end to end with `node bin/dossier-scribe.mjs demo`, which builds a throwaway repo in a temp directory. <!-- src: src/commands/demo.mjs:35, CONTRIBUTING.md:17 -->
- There is no build, lint, format or typecheck step: the only script is `test`. CI runs `node --test` on Node 22 and 24. <!-- src: package.json#scripts, .github/workflows/ci.yml:18-32 -->
- This repo's own docs are pinned, so after editing a cited file or a pinned doc run `node bin/dossier-scribe.mjs lint`, then `check`, then `pin --all`, and commit the docs and `dossiers/.dossier-lock.json`. CI runs the action against this repo. <!-- src: dossier-scribe.json, .github/workflows/ci.yml:34-41, src/lock.mjs:5 -->
- Regenerate the README terminal image with `node scripts/render-assets.mjs`. <!-- src: scripts/render-assets.mjs:1-2 -->
- Release by adding a `## [x.y.z] - date` section to CHANGELOG.md and running `npm version <patch|minor|major> --no-git-tag-version`; merging that publishes. <!-- src: CONTRIBUTING.md:42, .github/workflows/publish.yml:20-23 -->

## Conventions

- ES modules, Node 22 built-ins only, no runtime or dev dependencies. A test fails if `package.json` gains any. <!-- src: package.json#type, package.json#engines.node, test/cli.test.mjs:77-84 -->
- One module per command in `src/commands/`, each exporting `run`, and registered in the `COMMANDS` table in `src/cli.mjs`. <!-- src: src/cli.mjs:33-43, src/commands/lint.mjs:5 -->
- Exit codes: 0 clean, 1 findings, 2 usage or config error. <!-- src: src/cli.mjs:59-61, src/commands/check.mjs:37 -->
- The tool makes no network or model calls and never rewrites dossier prose. It pins, checks, regenerates the auto block and writes task files. <!-- src: CONTRIBUTING.md:8, src/dossiers.mjs:92-100 -->
- Tests build real temporary git repos and run the CLI as a black box. Git is never mocked. <!-- src: test/helpers.mjs:1, CONTRIBUTING.md:9 -->
- Write the failing test first. A change to a guard (env value redaction, range-only hashing, the queue cooldown) needs a test that goes red when the guard is weakened. <!-- src: CONTRIBUTING.md:20 -->
- No personal data, em dashes or attribution trailers anywhere in the tree, docs included. `test/hygiene.test.mjs` fails on the banned strings it lists. <!-- src: test/hygiene.test.mjs:9-32 -->
- Every commit carries a DCO sign-off (`git commit -s`). There is no CLA. <!-- src: CONTRIBUTING.md:26 -->
- Third-party actions are pinned by full SHA, and every workflow job declares least-privilege permissions. Tests enforce both. <!-- src: test/kit.test.mjs:36-55 -->
- The pinned docs are linked into `dossiers/` (`agents.md`, `architecture.md`) because the tool reads one flat folder. `requireCitations` in `dossier-scribe.json` lists the sections that must cite. <!-- src: dossier-scribe.json, src/dossiers.mjs:26-27 -->

## Traps

- Every git read is against `HEAD`. Uncommitted edits to a cited file are not drift, and a file that is not committed cannot be cited, so commit before you `lint` or `pin`. <!-- src: src/git.mjs:48-58, src/commands/pin.mjs:28 -->
- Cite the narrowest thing: a line range or a JSON key such as `package.json#engines.node`. A whole `package.json` changes every release and flags every claim that cites it. <!-- src: src/check.mjs:252 -->
- A claim is a list item or paragraph with exactly one source comment at its end, the HTML comment that starts `src:`. Two of them, or text after one, is a lint error. <!-- src: src/claims.mjs:49-55 -->
- A claim's id is a hash of its normalised text, so rewording a claim shows the old one as `REMOVED` and the new one as `NEW` until you re-pin. <!-- src: src/claims.mjs:15-17 -->
- The frontmatter parser takes plain `key: value` lines and flat inline lists only. Nesting, block lists and trailing comments are errors. <!-- src: src/frontmatter.mjs:1-7 -->
- The loader skips files starting with `.` or `_` and any `readme.md`, so those in `dossiers/` are never read as dossiers. <!-- src: src/dossiers.mjs:26-27 -->
- The weekly upkeep workflow runs `auto --check` over every file in `dossiers/`, and `auto` appends an auto block to any doc that has none. So AGENTS.md and docs/ARCHITECTURE.md carry a generated facts block: after a version or script change in `package.json`, run `node bin/dossier-scribe.mjs auto` on them or that job reports them out of date. <!-- src: src/commands/auto.mjs:15, .github/workflows/upkeep.yml:70 -->
- The weekly upkeep workflow installs dossier-scribe from a pinned release tag, not from the checkout, so a new command or flag is not seen there until the tag is bumped in a reviewed change. <!-- src: .github/workflows/upkeep.yml:62 -->
- Merging a change to `package.json` on main starts the publish workflow, which releases any version npm or GitHub does not have yet. Do not bump the version by accident. <!-- src: .github/workflows/publish.yml:20-23, scripts/release.mjs:33-47 -->
- `package.json#files` lists what npm ships: `bin`, `src`, `examples`, LICENSE and README.md. `docs/` and `dossiers/` are not in the package. <!-- src: package.json#files -->

## Unverified

- Whether Windows works. The README says it is untested. <!-- src: README.md:68 -->

## Generated facts

<!-- auto:start -->
- package: dossier-scribe 0.2.1
- npm scripts (1): test
<!-- auto:end -->
