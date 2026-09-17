<div align="center">

<img src="webui/logo/nova.png" alt="Nova logo" width="150" />

<h1>Nova</h1>

**A teaching and learning assistant for NLP courses.**

<p>
  <img src="https://img.shields.io/github/stars/liunor/nlp-agent?logo=github" alt="GitHub stars">
  <img src="https://github.com/liunor/nlp-agent/actions/workflows/ci.yml/badge.svg?branch=main" alt="CI">
  <img src="https://img.shields.io/badge/Python-3.11%2B-3776AB?logo=python&logoColor=white" alt="Python 3.11+">
  <img src="https://img.shields.io/badge/FastAPI-005571?logo=fastapi" alt="FastAPI">
  <img src="https://img.shields.io/badge/LangGraph-1C3C3C" alt="LangGraph">
  <img src="https://img.shields.io/badge/MySQL-4479A1?logo=mysql&logoColor=white" alt="MySQL">
  <img src="https://img.shields.io/badge/Redis-DC382D?logo=redis&logoColor=white" alt="Redis">
  <img src="https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white" alt="Docker">
  <img src="https://img.shields.io/badge/docs-docs%2F-blue" alt="Docs">
  <img src="https://img.shields.io/badge/License-MIT-0969da" alt="MIT License">
</p>

<p>
  <a href="#features">Features</a> ·
  <a href="#quick-start">Quick Start</a> ·
  <a href="README.zh.md">中文</a> ·
  <a href="CONTRIBUTING.md">Contributing</a> ·
  <a href="LICENSE">License</a>
</p>

</div>

<img src="docs/assets/demo-flow.gif" alt="Nova feature walkthrough" width="100%" />

Nova is an assistant for NLP teaching and learning. Teachers write topics, knowledge points and exercises; students ask questions and get explanations, practice and review. Each exercise answer is graded against a rubric the teacher defines.

## Start here

| You want to... | Go to |
| --- | --- |
| Run it locally in minutes | [Quick Start](#quick-start) |
| See what it looks like | [Four dedicated views](#four-dedicated-views) |
| Understand the feature set | [Features](#features) |
| Configure models & database | [`.env-example`](./.env-example) |
| Contribute or extend it | [CONTRIBUTING.md](./CONTRIBUTING.md) |

## Four dedicated views

Nova runs four role-based views over a single deployment, so a whole classroom works on one instance:

### Learner

Ask questions in plain language, get step-by-step explanations, practice with auto-graded exercises, and review each answer against the rubric your teacher defined.

<p align="center">
  <img src="docs/assets/screenshot-learner.png" alt="Learner view" width="900">
</p>

### Teacher

Author topics and Markdown knowledge points that are injected into exactly the scope a student's question needs, and design exercise blueprints that generate questions and grade answers against weighted rubrics.

<p align="center">
  <img src="docs/assets/screenshot-teacher.png" alt="Teacher view" width="900">
</p>

### Developer

Manage model providers and keys, runtime configuration, and the sandbox that runs generated code, all from one place.

<p align="center">
  <img src="docs/assets/screenshot-developer.png" alt="Developer view" width="900">
</p>

### Monitor

Watch live turns, traces, and metrics across the web, worker, and sandbox, and inspect task activity for the whole pipeline.

<p align="center">
  <img src="docs/assets/screenshot-monitor.png" alt="Monitor view" width="900">
</p>

## Features

- **Four role-based views**: Learner, Teacher, Developer and Monitor, separated by access control.
- **Guided learning**: question-and-answer sessions that work step by step.
- **Auto-graded exercises**: teachers define blueprints that generate questions and grade answers against weighted rubrics.
- **Knowledge-point catalogue**: teachers write topics and Markdown knowledge points; each prompt gets exactly the scope it needs.
- **Observability**: a built-in monitor shows turns, traces and metrics across web, worker and sandbox.
- **Modular runtime**: a coordinator/worker engine on LangGraph, with tools, memory and sandboxed code execution.
- **Web and CLI**: chat from the browser or the terminal.

## Quick Start

### Prerequisites

- Python 3.11 or newer, and [uv](https://docs.astral.sh/uv/).
- A MySQL database, configured in `.env`.

> [!NOTE]
> Copy [`.env-example`](./.env-example) to `.env` and fill in the model service key and database connection before starting.

> [!TIP]
> For the full distributed stack (nginx, MySQL, Redis, web, worker, sandbox manager), use the provided [`compose.yaml`](./compose.yaml).

### Install and run

```powershell
uv sync                                   # install dependencies
Copy-Item .env-example .env               # prepare config: model key & database
uv run python main.py bootstrap-db        # initialize the database
uv run python main.py bootstrap-developer # create the first account
uv run python main.py serve               # start the server
```

Once it starts, open <http://127.0.0.1:8765>.

### Log in and explore

Log in with the account created by `bootstrap-developer`:

- Learner — <http://127.0.0.1:8765/>
- Teacher — <http://127.0.0.1:8765/teacher>
- Developer — <http://127.0.0.1:8765/developer>

Or chat from the terminal:

```powershell
uv run python main.py chat
```

For the observability monitor, run `uv run python main.py monitor` first, then open <http://127.0.0.1:8766/>.

## Documentation

- [Contributing](./CONTRIBUTING.md)
- [Configuration & guides](./docs/)
- [License](./LICENSE)

---

This repository is licensed under the [MIT](./LICENSE) license.