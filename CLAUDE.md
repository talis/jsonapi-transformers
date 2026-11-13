# CLAUDE.md

`@talisdev/jsonapi-transformers` is a TypeScript library that converts between [JSON:API](https://jsonapi.org/) documents and decorated TypeScript classes. It has no HTTP layer; it only serialises and deserialises. It is published to the public npm registry and consumed by the Talis front-end repos (for example `shipshape-app` and `otis-app`). Default branch: `main`.

**Risk:** a release is published to the public npm registry and reaches every consumer on its next dependency bump, so a wrong version or a breaking change is hard to take back.

## Layout

- `src/decorators/` - `@Entity`, `@Attribute`, `@Relationship`, `@Link`, `@Meta` and the metadata map they fill.
- `src/jsonapi/` - JSON:API types, the base entity class, unresolved identifiers.
- `src/serialisation/` - serialisers and deserialisers.
- `spec/` - Jest tests, with fixtures in `spec/test-data/` (classes) and `spec/test-data/jsonapi/` (JSON:API documents).

## Setup and commands

Standalone repo: no other Talis repo and no local services. Run from the repo root with the Node version in `.nvmrc` (`nvm use`), after `npm ci` (which also runs `prepare`, and so the build).

| Task | Command |
|---|---|
| Unit tests (with coverage) | `npm test` |
| Lint (eslint + prettier check) | `npm run lint` |
| Format | `npm run format` |
| Build both module formats into `lib/` | `npm run build` |
| Validate fixture documents against the JSON:API schema | `npm run validate_jsonapi_examples` |

CI (CircleCI) runs, in order: commit message lint, `npm run lint`, `npm run validate_jsonapi_examples`, `npm test`, `npm run build`. Run the same set before opening a PR. The suite needs no services, so a full local run is fine. Run one spec with `npx jest spec/serialisation/serialisers.spec.ts`.

## Conventions

- Source is TypeScript, formatted by prettier and linted by eslint. Match the style of the closest existing file.
- New behaviour needs a spec next to its siblings in `spec/`. If it changes the JSON:API output, add or update a fixture in `spec/test-data/jsonapi/`; `validate_jsonapi_examples` checks every file there against `spec/jsonapi-schema.json`.
- Record public API changes that break consumers in `BREAKING_CHANGES.md` (maintained by hand).
- Do not edit `lib/` (git-ignored build output), `CHANGELOG.md` or the `version` in `package.json` (release-please writes them), or `package-lock.json` by hand.

## Guardrails

- **Claude may run:** `npm ci`, `npm run lint`, `npm test`, `npm run build`, `npm run validate_jsonapi_examples`, and read-only `git` commands.
- **Ask first:** anything that merges or tags. Merging the release PR that release-please opens publishes to npm, so a person does that.
- **Never:** `npm publish` by hand, or edit the version, changelog or tags to force a release. Only `.github/workflows/release-please.yml` publishes.

## Commit and PR titles decide the release

Releases are automated with release-please: merging to `main` opens or updates a release PR, and merging that publishes to npm.

- Commit messages and PR titles follow [Conventional Commits](https://www.conventionalcommits.org/) (`feat:`, `fix:`, `chore:`, `feat!:` for breaking). CI runs commitlint on the latest commit message, and a workflow lints the PR title (skipped for PRs labelled `dependencies`).
- The prefix chooses the version bump, so a wrong prefix ships the wrong version.

## Traps

- `README.markdown` is partly stale: it says Node 14-18 and shows `npm install jsonapi-transformers`. The package name is `@talisdev/jsonapi-transformers`, `engines` allows Node `>=14 <25`, and CI uses the version in `.nvmrc`. `MIGRATION.md` explains the move from the old unscoped name. Do not copy the old name into new docs.
- The package must keep working as both CommonJS and ES modules. `scripts/fixup` writes the `package.json` type markers into `lib/cjs` and `lib/mjs`; keep it in the build.
- Dependency updates come from Dependabot (configured centrally, not in this repo), which replaced Renovate. Do not re-add `renovate.json`.
- Check the npm registry directly (`curl -s https://registry.npmjs.org/@talisdev%2fjsonapi-transformers`) for published versions; `npm view` has returned stale data for this package.

## Where other things live

- Workspace guidance (what the other Talis repos are, safety rules for remote operations): `CLAUDE.md` in the workspace root repo `talis/talis-dev-root`, at `$DEVELOPMENT_WORK_DIR`.
- `README.markdown` - worked blog example of the decorators. `MIGRATION.md`, `BREAKING_CHANGES.md` - consumer-facing changes.

## PR conventions

- Branch from `main`, one change per PR. Put the ticket key in the conventional-commit scope when there is one, as recent history does (for example `chore(TA-3890): ...`).
- The description says what changed, why, and how it was tested. CI (CircleCI) must pass.
- Open as a draft when Claude-authored; a human reviews and merges.

## Skills

- `talis:ci` - Talis rules for reading and re-running CircleCI.
- `talis:pr-review-feedback` - work through review comments on a PR.
- `talis:npm-cve-patch` - patch npm audit findings; the fix still needs a conventional-commit title here.
- `talis:node-runtime-bump` - move to a newer Node version consistently.

## Keeping this file current

If a change alters anything described here (commands, release flow, layout), update this file in the same PR. Link detail rather than copying it.
