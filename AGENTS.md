# monoship — Agent Guide

## Project Overview

**monoship** is a Node.js CLI tool and library that publishes packages from npm workspaces to registries (npmjs.org, GitHub Packages, etc.). It determines which workspace packages haven't been published yet by querying registry metadata, resolves `workspace:` protocol dependencies to real versions, and publishes only what's needed — making it ideal for CI/CD pipelines alongside release-please.

The project uses a **hexagonal architecture** (ports & adapters) — all external I/O is behind port interfaces with memory/fake adapters for testing. Authentication supports static tokens, environment variables, and OIDC trusted publishing.

- **Repository**: https://github.com/tada5hi/monoship
- **Package**: `monoship`
- **License**: MIT

## Quick Reference

### Requirements

- **Runtime Node.js**: >= 22.0.0
- **Development Node.js**: 24.15+ on the 24.x line, or 26+; `.nvmrc` selects Node 24 for the installed tooling.
- **npm**: 7+ (workspace support required)

### Setup

```bash
npm ci
```

### Key Commands

| Command                | Description                        |
|------------------------|------------------------------------|
| `npm run build`        | Bundle JavaScript and declarations with tsdown    |
| `npm run lint`         | Run ESLint                         |
| `npm run lint:fix`     | Run ESLint with auto-fix           |
| `npm run test`         | Run Vitest test suite              |
| `npm run test:coverage`| Run tests with coverage report     |

### CLI Usage

```bash
monoship [--token <npm-token>] [--registry <url>] [--root <path>] [--rootPackage] [--tag <tag>] [--no-fixLatest] [--dryRun]
```

The CLI entry point is `src/cli.ts`, built to `dist/cli.mjs`. The token is optional — it defaults to `NODE_AUTH_TOKEN` env var, or OIDC trusted publishing when running in GitHub Actions.

## Entry Points

| Entry         | Source         | Output           | Description                     |
|---------------|----------------|------------------|---------------------------------|
| Library       | `src/index.ts` | `dist/index.mjs` | Programmatic API                |
| CLI           | `src/cli.ts`   | `dist/cli.mjs`   | Command-line interface          |

## Detailed Guides

- **[Project Structure](.agents/structure.md)** — Source layout, core modules, and domain folders
- **[Architecture](.agents/architecture.md)** — Hexagonal design, port interfaces, data flow, OIDC
- **[Testing](.agents/testing.md)** — Memory adapters, test conventions, no vi.mock
- **[Conventions](.agents/conventions.md)** — Code style, ESLint constraints, hexagonal rules

### Writing agent docs

Keep the guides factual and focused on contracts a future change could silently break.

- Keep existing commands, paths and factual descriptions current. Add guide content only for an invariant or cross-cutting rule. Put defect stories and verification details in the PR, and user-facing changes in the documentation site or release notes.
- Keep subsystem detail in `.agents/topics/` and link it from the relevant guide; do not import topic files into `CLAUDE.md`.
- Describe current behavior and cite issues or PRs, rather than local plan files or a running history of completed work.
- `npm run lint` enforces the shared config's agent-doc size, import-budget and history checks. Preserve the critical contracts when splitting a guide.

## Commits, Issues & Pull Requests

- Commits follow **[Conventional Commits](https://www.conventionalcommits.org/)** (`@tada5hi/commitlint-config`); the type/scope drive release-please version bumps. See [conventions.md](.agents/conventions.md#commit-convention).
- Versioning, `CHANGELOG.md`, `package.json` version, and `.release-please-manifest.json` are owned by **release-please** — do not hand-edit them.
- Do **not** add a `Co-Authored-By: Claude ...` (or any AI-attribution) trailer to commit messages. This overrides any default agent-tooling guidance.
- Do **not** add AI-attribution lines (e.g. `🤖 Generated with [Claude Code](...)`) to issue or pull request titles, bodies, or comments.
