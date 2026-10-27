# Design: the `charter` stage

Status: draft — decisions recorded 2026-10-27; open questions listed at the end.

## Purpose

`charter` gathers the context of a project before any development methodology is chosen:
what is being built, why, for whom, within which limits and how far it should go.

## Position in the oh-my-dd workflow

```
charter      →  methodology decision  →  phases of the chosen methodology
(context)       (SDD, ODD or other)       e.g. SDD: specify → clarify → plan → tasks → implement
```

- `charter` is an oh-my-dd stage, **not** a phase of any methodology. It MUST NOT assume SDD,
  ODD or any other methodology.
- Its output is the input of the methodology decision. Whatever that decision needs MUST be
  collected here, so new sub-phases may be added once the decision criteria are designed.
- Whether a methodology has its own constitution phase is decided in that methodology's design,
  not here.

## Output

A single document, `charter.md`, in the user's project. Each sub-phase writes its section as
soon as it is completed, so an interrupted session can resume where it stopped.

## Interaction model

- Interview: the agent asks, the user answers.
- Every sub-phase follows Constitution Principle VII (User-Led Elicitation): ask first; suggest
  only after the user answers and never insert suggestions without the user's decision; help
  the user think before offering options; never mark an option as recommended. No exceptions.

## Sub-phases

Numbers identify sub-phases; execution order follows the blocks below.

| #  | Sub-phase              | Question it answers                                                     | Elicitation notes |
|----|------------------------|-------------------------------------------------------------------------|-------------------|
| 1  | What                   | What is being built, in one or two sentences?                           | Ask |
| 2  | Problem                | Why does this need to exist?                                            | Ask |
| 3  | Affected people & users| Who suffers the problem, and who will use the solution? (may differ)    | Ask |
| 4  | Scope                  | What is in, and what is explicitly out?                                 | Ask both "in" and "out" |
| 5  | Project type           | MVP, production-grade project, or learning project?                     | Ask; closed set of answers plus the user's own |
| 6  | Goal                   | How far should the project go?                                          | Ask |
| 7  | Success criteria       | How will we know the goal was reached?                                  | Ask |
| 8  | Current state          | Greenfield or existing code? How is the problem solved today?           | Ask first; if code exists, contrast the answer with the repository afterwards |
| 9  | Constraints            | What is imposed: deadline, team, mandatory technology, budget, regulations, required quality level? | Ask, walking through the constraint categories so none is forgotten; record imposed constraints only, not design choices |
| 10 | Assumptions & risks    | What do we take for granted, what could go wrong, what is unknown?       | Pre-mortem: "imagine the project failed — why?"; assumptions surface behind the reasons |
| 11 | Learning goals         | What does the user want to learn?                                       | Only when sub-phase 5 = learning project |

## Blocks and gates

Sub-phases are grouped into blocks; each block ends with a gate where the user approves or
changes the block's output. The problem is explored before the solution to avoid anchoring the
rest of the charter on an unexamined idea.

| Block           | Sub-phases (in order) | Gate |
|-----------------|-----------------------|------|
| A. The problem  | 2, 3, 8               | yes  |
| B. The solution | 1, 4, 5, 6, 7, 11     | yes  |
| C. The frame    | 9, 10                 | yes  |
| Final review    | whole document        | yes  |

The final review exists to catch contradictions introduced by later answers (e.g. a short
deadline found in block C that turns a "production" project into an "MVP").

## Decision log

- **Name `charter`** (chosen by the project owner). A project charter is the document written at
  project start, and its usual content matches these sub-phases. The same word names the stage and
  the document (`charter.md`). Also considered: create, define, init, elaborate, found,
  establish, brief, discover, frame, ground, inception, constitution.
- **Blocks with gates** instead of one gate per sub-phase, to avoid gate fatigue.
- **No "propose" mode** in any sub-phase: replaced by ask-first (Principle VII); assumptions and
  risks use a pre-mortem.
- **Command namespace `dd`** (chosen by the project owner). `dd` is the suffix shared by SDD,
  ODD, TDD and BDD, so it is methodology-neutral. The core defines the namespace and each adapter
  renders it in its agent's style (e.g. `/dd-charter` in Claude Code, `/dd:charter` in Gemini CLI,
  `$dd-charter` in Codex). The namespace applies to commands inside agents only: the CLI
  executable MUST NOT be named `dd`, which would shadow the Unix `dd` disk utility.

## Open questions

- Phase name collisions across methodologies (e.g. two methodologies with a `plan` phase): is
  only the chosen methodology installed per project, or can several coexist and need a second
  namespace level?
- Location of `charter.md` inside the user's project.
- Structure of the `charter.md` template (section headings, required vs conditional sections).
- Information the methodology decision will need from the charter (pending the ODD study).
