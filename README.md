<div align="center">

# 🧭 oh-my-dd

### Choose and tailor the right *-driven development workflow for your AI coding agent

[![Status](https://img.shields.io/badge/Status-Design%20phase-F59E0B?style=for-the-badge)](#-status)
[![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![uv](https://img.shields.io/badge/uv-DE5FE9?style=for-the-badge&logo=uv&logoColor=white)](https://docs.astral.sh/uv/)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-D97757?style=for-the-badge&logo=claude&logoColor=white)](https://claude.com/claude-code)
[![License](https://img.shields.io/badge/License-Apache%202.0-D22128?style=for-the-badge&logo=apache&logoColor=white)](./LICENSE)

</div>

---

## ✨ Overview

**oh-my-dd** is a CLI that plugs into the harness of AI coding agents (Claude Code, Codex CLI,
Gemini CLI) right when a project starts. It gathers the project's context, helps decide which
development methodology fits it best (Spec-Driven Development, Observability-Driven Development,
or others) and installs that methodology as a workflow the agent can follow and the user can
tailor.

oh-my-dd is an independent project: its methodologies are built from scratch, not on top of
existing toolkits.

## 🔄 How it works

```
charter      →  methodology decision  →  phases of the chosen methodology
(context)       (SDD, ODD or other)       e.g. SDD: specify → clarify → plan → tasks → implement
```

| Stage | What happens |
|---|---|
| 📝 **charter** | The agent interviews the user to capture what is being built, the problem, who it affects, scope, project type, goal, success criteria, current state, constraints, assumptions and risks. The result is `charter.md`. |
| ⚖️ **Methodology decision** | The charter's context is used to decide which methodology the project should follow. |
| 🛠️ **Methodology phases** | The chosen methodology's phases guide the development, e.g. SDD's specify → clarify → plan → tasks → implement. |

Inside the agent, every oh-my-dd command lives under the `dd` namespace, e.g. `/dd-charter`.

### 🗣️ The user stays in charge

Every stage follows the same interaction rule: **the agent asks first and never decides for
you.** Suggestions come only after your answer and never enter the output without your decision;
options are offered only as a last resort, and none is ever marked as "recommended". This avoids
anchoring you on the agent's ideas.

## 🤖 Supported agents

| Agent | Status |
|---|---|
| <img src="https://cdn.simpleicons.org/claude" width="16"> Claude Code | 🚧 First integration, in design |
| <img src="https://cdn.simpleicons.org/googlegemini" width="16"> Gemini CLI | 🗓️ Planned |
| Codex CLI | 🗓️ Planned |

Integrations are built one at a time: each one must work end-to-end before the next one starts.

## 🧱 Design principles

The full rules live in the [project constitution](./.specify/memory/constitution.md).

| # | Principle | In one line |
|---|---|---|
| I | 🧩 Agent-agnostic core | Agent-specific details live only in per-agent adapters. |
| II | 🔒 User files are sacred | Files the user modified are never overwritten without confirmation. |
| III | ⚙️ Deterministic work belongs to the CLI | Mechanical steps are CLI subcommands; the model only does what needs judgment. |
| IV | 🧪 Test-first, strictly | Strict TDD, with snapshot tests for the CLI and evals for skills. |
| V | 🎯 One integration end-to-end before the next | Depth before breadth; YAGNI. |
| VI | 💻 Cross-platform parity | Identical behavior on Windows, macOS and Linux. |
| VII | 🗣️ User-led elicitation | Ask first; never insert suggestions or mark recommendations. |

## 🚦 Status

oh-my-dd is in its **design phase**: there is nothing to install yet.

- [x] Project constitution (v1.1.0)
- [x] Design of the `charter` stage ([docs/design/charter-phase.md](./docs/design/charter-phase.md))
- [ ] Design of the SDD phases
- [ ] CLI (Python, distributed with uv)
- [ ] Claude Code integration
- [ ] Methodology decision
- [ ] Observability-Driven Development
- [ ] Codex CLI and Gemini CLI integrations

## 🗂️ Repository layout

```
.
├── docs/design/          → Design documents of oh-my-dd's stages and phases
├── .specify/             → Development tooling (Spec Kit): constitution, templates
├── .claude/skills/       → Development tooling (Spec Kit skills for Claude Code)
└── LICENSE
```

This repository is itself developed with Spec-Driven Development, using
[GitHub Spec Kit](https://github.com/github/spec-kit) **as development tooling only**: oh-my-dd
does not depend on it, and none of its files are shipped with the product.

## 🤝 Development

- `staging` is the development branch; `main` receives changes through pull requests.
- Everything in the repository is written in English.

## 📜 License

Released under the [Apache License 2.0](./LICENSE).
