# Contributing

Thanks for your interest in Nova! This guide helps you ship your first contribution — whether it's a typo fix or a new feature.

> 中文版见 [CONTRIBUTING.zh.md](./CONTRIBUTING.zh.md)。

## Table of contents

- [Code of Conduct](#code-of-conduct)
- [What can I contribute](#what-can-i-contribute)
- [Development environment](#development-environment)
- [Branch model](#branch-model)
- [Commit conventions](#commit-conventions)
- [Submitting a Pull Request](#submitting-a-pull-request)
- [Code style and tests](#code-style-and-tests)

## Code of Conduct

Be respectful and critique ideas, not people. Keep communication honest and constructive. Insulting, discriminatory or aggressive content will be rejected.

## What can I contribute

- **Fix docs / typos**: open a PR directly, typed `docs:`.
- **Fix a bug**: confirm a stable reproduction first, and describe the trigger and user impact in the PR.
- **Add a feature**: explain the motivation and approach in an Issue first and reach agreement before coding, to avoid rework.
- **Add evaluation / tests**: test cases for tool routing, orchestration and guided teaching are welcome — see [`evaluation/`](evaluation/).

## Development environment

Prerequisites: Python 3.11+, [uv](https://docs.astral.sh/uv/), MySQL 8.x (Redis optional).

```powershell
uv sync
Copy-Item .env-example .env   # fill in the model key & database connection
uv run python main.py bootstrap-db        # set up the schema
uv run python main.py bootstrap-developer # create the first developer account
uv run python main.py serve               # start the web server
```

The frontends (learner + monitor) live in `webui/`:

```powershell
cd webui
npm install
npm run dev            # learner Vite on :5173
npm run dev:monitor    # monitor Vite on :5174
```

## Branch model

The project follows Git Flow — see [`git-flow 开发流程.md`](git-flow%20开发流程.md):

```text
feature/* ──PR──► develop ──PR──► main
```

- Branch `feature/<topic>` or `fix/<topic>` from `develop`.
- Open a PR against `develop` after local verification.
- `main` is protected and only accepts merges from `develop`.

## Commit conventions

Commit messages follow [Conventional Commits](https://www.conventionalcommits.org/), consistent with the repo history:

```text
<type>(<scope>): <one-line summary>

optional body and footer
```

Common types:

| type | purpose |
| --- | --- |
| `feat` | new feature |
| `fix` | bug fix |
| `docs` | documentation |
| `refactor` | refactor (no behavior change) |
| `test` | test additions / fixes |
| `chore` | build, dependencies, misc |

> Schema changes must go through Alembic migrations exclusively (see [`docs/migrations.md`](docs/migrations.md)); the app never calls `create_all`, so don't include hand-written DDL in PRs.

## Submitting a Pull Request

1. Run local verification first (see "Code style and tests" below) and make sure nothing fails.
2. Fill in the 7-section structure from [`.github/pull_request_template.md`](.github/pull_request_template.md): summary, background & motivation, changes, implementation, files touched, test & verification, impact & risk.
3. Tick the Reviewer Checklist and fill in "uncovered items" honestly — write "none" when there are none.
4. Wait for CI (backend tests + lint, frontend lint/test/build, Docker build check) to pass.

## Code style and tests

Backend:

```powershell
uv run ruff check configs core gateway server tests scripts migrations
uv run pytest
```

Frontend:

```powershell
cd webui
npm run typecheck
npm run lint
npm test
npm run build
npm run build:monitor
```

You don't have to pass every test before submitting, but be honest in the PR about what isn't covered and why.