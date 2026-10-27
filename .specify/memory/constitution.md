# oh-my-dd Constitution

## Core Principles

### I. Agent-Agnostic Core

- The core (methodology content, workflow logic, manifest handling, CLI commands) MUST NOT
  reference any concrete agent: no agent-specific paths (e.g. `.claude/`, `.gemini/`,
  `.codex/`, `.agents/`), context file names (e.g. `CLAUDE.md`, `GEMINI.md`, `AGENTS.md`), or
  command/skill formats may appear outside that agent's adapter.
- Methodology content MUST have a single source of truth authored in an agent-neutral form.
  The Agent Skills standard (`SKILL.md`) is the default authoring format wherever the target
  agents support it.
- Each agent adapter MUST be the only module that translates neutral content into that agent's
  layout and formats. Adding or removing an agent MUST NOT require changes to the core or to
  methodology content.
- An automated test MUST fail when agent-specific identifiers appear outside the adapters.

**Rationale**: oh-my-dd targets several agents (Claude Code, Codex CLI, Gemini CLI). Content is
expensive to write and maintain; adapters are cheap. Keeping one source of truth prevents the
methodology from forking per agent.

### II. User Files Are Sacred

- The CLI MUST record every file it writes in an install manifest together with a content hash.
- Before overwriting or deleting a file, the CLI MUST compare the file's current hash with the
  manifest. If the file was modified by the user, or exists but is not tracked by the manifest,
  the CLI MUST NOT overwrite or delete it without explicit user confirmation.
- Explicit confirmation means an interactive answer or an explicit command-line flag. In the
  absence of confirmation, the file MUST be left untouched and reported to the user.
- Hashes MUST be stable across platforms (e.g. unaffected by line-ending conversion).

**Rationale**: oh-my-dd writes into other people's repositories. A single lost edit destroys
trust in the tool; safe defaults are mandatory, not optional.

### III. Deterministic Work Belongs to the CLI

- Every mechanical operation (creating directories, numbering features, resolving and rendering
  templates, validating artifact structure, reading and writing the manifest) MUST be
  implemented as a CLI subcommand in Python.
- The product MUST NOT ship shell scripts (`.sh`, `.ps1`, `.bat` or similar). Skills invoke the
  CLI instead.
- Prompts and skills MUST only ask the model for work that requires judgment (understanding
  intent, writing specifications, making design decisions, reviewing). When a skill needs a
  mechanical step, it MUST call the corresponding CLI subcommand.
- CLI subcommands invoked by skills SHOULD offer machine-readable (JSON) output so skills can
  consume results without parsing human-oriented text.

**Rationale**: deterministic code is testable, cheap and cross-platform by construction; model
output is none of those. Moving mechanics into the CLI reduces variance, token cost, and the
need to maintain parallel shell implementations.

### IV. Test-First, Strictly (NON-NEGOTIABLE)

- Test-Driven Development is mandatory for all production code and all product skills/prompts:
  write the test → run it and observe it fail → write the minimal implementation → refactor.
- Bug fixes MUST start with a failing test that reproduces the bug.
- Two test layers are REQUIRED:
  - **CLI tests**: unit and integration tests. Every command that writes files MUST have
    snapshot (golden) tests that run the command in a temporary directory and compare the full
    resulting file tree (paths and contents) against committed expected output. Changes to
    golden files MUST be explicit and reviewed; they MUST NOT be auto-accepted.
  - **Skill evals**: every skill or prompt shipped by the product MUST have evals that run it
    against one or more fixture projects and check explicit, recorded criteria about the result.
    The eval MUST exist and fail before the skill (or the skill change) is written.
- In every `tasks.md`, test and eval tasks MUST be ordered before the implementation tasks they
  cover.

**Rationale**: the product is a generator of files and prompts; without golden tests and evals,
regressions in what it writes or in how agents behave are invisible until a user hits them.

### V. One Integration End-to-End Before the Next

- Claude Code is the first integration. A new agent integration MUST NOT be merged until the
  previous one works end-to-end: install, the full SDD flow, upgrade and removal (respecting
  Principle II), all covered by tests and evals.
- YAGNI: no feature, option, abstraction or extension point may be added without a current
  specification that requires it. The adapter boundary required by Principle I is the only
  abstraction mandated in advance.
- Any added complexity, including each new runtime dependency, MUST be justified in the
  feature plan; unjustified complexity is a constitution violation.

**Rationale**: breadth before depth produces three half-working integrations. One complete
vertical slice validates the architecture before it is replicated.

### VI. Cross-Platform Parity

- The product MUST behave identically on Windows, macOS and Linux: same commands, same flags,
  and byte-identical generated files.
- CI MUST run the full test suite on all three operating systems; a change that fails on any of
  them MUST NOT be merged.
- Paths MUST be handled through platform-independent APIs (e.g. `pathlib`); hard-coded path
  separators and shell-dependent invocations are forbidden.
- Every file in the repository and every file the product generates MUST use UTF-8 encoding and
  LF line endings.

**Rationale**: the primary developer works on Windows while most agent tooling assumes Unix.
Parity must be enforced explicitly or it silently erodes.

## Technology & Distribution Constraints

- **Language**: Python. The minimum supported Python version MUST be declared in
  `pyproject.toml` and exercised in CI; its value is decided in the first feature plan.
- **Packaging**: the project is a uv project (`pyproject.toml` and `uv.lock` committed). The CLI
  MUST be installable with `uv tool install` and runnable with `uvx` without additional setup.
- **Independence**: oh-my-dd is an independent product. It MUST NOT import, vendor, wrap or
  depend on GitHub Spec Kit. The Spec Kit files in this repository (`.specify/`,
  `.claude/skills/speckit-*`) are development tooling only and MUST NOT be shipped in the package.
- **Content location**: product methodology content (skills, templates, workflow definitions)
  MUST live in the package source tree, never in this repository's agent folders (e.g.
  `.claude/`), which belong to the development tooling.
- **Language of artifacts**: everything in the repository MUST be written in English: code,
  comments, specifications, plans, tasks, documentation, product prompts and skills, commit
  messages and pull request descriptions. Conversations with contributors may use any language.

## Development Workflow & Quality Gates

- All work MUST go through Spec-Driven Development using this repository's tooling:
  specify → clarify → plan → tasks → implement. Clarify MAY be skipped only when the
  specification contains no open ambiguities.
- Every plan MUST pass the Constitution Check before research and again after design. Any
  violation MUST be justified in the plan's complexity section or the plan is rejected.
- A change MUST NOT be merged unless:
  - all tests pass on Windows, macOS and Linux;
  - evals pass for every skill or prompt the change touches;
  - every golden-file change has been explicitly reviewed;
  - the automated agent-agnostic check (Principle I) passes.

## Governance

- **Scope**: this constitution governs the development of oh-my-dd itself. The "constitution"
  concept the product may generate for users' projects is a separate future feature; it MUST be
  specified through its own specification, and this file MUST NOT be used as its template.
- **Supremacy**: this constitution supersedes all other practices in this repository. When a
  spec, plan, task list or review conflicts with it, the constitution wins.
- **Amendments**: amendments are made through `/speckit-constitution`, MUST include a rationale,
  and MUST be approved by the project owner. Artifacts in progress MUST be re-checked against the
  amended version.
- **Versioning**: semantic versioning. MAJOR for removing or redefining a principle in a
  backward-incompatible way; MINOR for adding a principle or section or materially expanding
  guidance; PATCH for clarifications and wording fixes.
- **Compliance**: every plan's Constitution Check and every `/speckit-analyze` run verify
  compliance; violations of a MUST are CRITICAL. Reviews MUST confirm compliance before merge.

**Version**: 1.0.0 | **Ratified**: 2026-10-27 | **Last Amended**: 2026-10-27
