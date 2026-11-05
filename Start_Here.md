# Start Here — the Deltra Software Factory

| Attribute | Value |
|---|---|
| Version | 1.1 — Approved |
| Date | 2026-11-04 |
| Owner | Deltra Systems LLC — Software Development |
| Audience | Anyone who builds, reviews, or approves Deltra web applications |

| Version | Date | Change |
|---|---|---|
| 1.0 | 2026-11-04 | Initial orientation guide |
| 1.1 | 2026-11-04 | Added the glossary (Section 8), the "If you are the Approver" section (Section 7), and Git rows in Section 5; rewrote the first-week exercise step by step (Section 9); added the "start a new application" row in Section 5 |

> **CONFIDENTIAL — Deltra Systems LLC. Internal engineering reference.**

This page is a guide, not a rulebook. It points to the documents that govern; it does not repeat their rules. Where this page and a governing document disagree, the governing document wins. Unfamiliar words are explained in the glossary (Section 8).

## 1. What the factory is

Deltra builds web applications the way a factory builds products. The **fixed plant** stays the same for every application: the governing documents in this repository, the reference template (`deltra-webapp-template`), its quality gates, and its Reviewer Agents. For each new application, the plant is **retooled**: a new Software Requirements Specification (SRS) is written, the template is copied and renamed, and the application's own business code replaces the template's example. Claude Code agents do the production work. One accountable human, the Approver, accepts every milestone, releases, and deploys.

## 2. Who does what

| Role | Who | Does | Governing section |
|---|---|---|---|
| **Approver** | The CEO | Approves requirements, ratifies agent decisions, performs acceptance testing, releases, deploys, and changes agent instructions and standards | AI Policy 26A.1, 81.6 |
| **Builder** | A Claude Code session on Claude Opus, supervised by a developer when one is assigned | Writes tests, code, migrations, and documents, one milestone at a time | AI Policy 26A.1; the application's `CLAUDE.md` |
| **Reviewer Agents** | Four read-only Claude Code subagents on Claude Sonnet | Check each change against the SRS, security, database safety, and document consistency | Stack 18; AI Policy 26A.2 |

Roles are defined by function. A human developer works in the Builder role under the same rules as the agents. A second qualified person also triggers a review of single-approver operation (Change Management Standard 39).

## 3. How work flows

```text
SRS approved ─► Requirements Gate
   │
   ▼  for each milestone (SRS Section 11)
Builder: branch ─► tests ─► code ─► decisions logged as Assumed ─► make check
   │
   ▼
Pull Request ─► CI runs make check ─► Reviewer Agents report ─► squash merge on sign-off
   │
   ▼
Evidence package ─► Acceptance Gate (Approver tests and ratifies) ─► … next milestone
   │
   ▼
Release Gate ─► Approver tags and deploys by the runbook
```

Workflow: Change Management Standard 134. Gates: 134A. Evidence package: 134B. Git: 134C.

## 4. Rules that are never broken

1. **Synthetic data only in development.** Real data appears only in production. — AI Policy 26A.7
2. **No production access from a development machine.** Agents and developers never hold production credentials; only the Approver deploys. — AI Policy 26A.9; Stack 15
3. **Stop and ask the Approver** before anything on the required-stops list. — AI Policy 26A.5
4. **Open business decisions don't stop work:** decide, record as Assumed, continue. Never decide silently. — AI Policy 26A.4
5. **Nothing reaches `main`** except through a Pull Request with a passing `make check` and sign-off from each required Reviewer Agent. — Change Management Standard 44, 48, 134C
6. **Every change cites the requirement IDs it serves, and every requirement has a test.** — SRS Section 1.3
7. **Part A of the Stack is not optional.** A needed deviation is proposed in an ADR and decided by the Approver. — Stack 2
8. **Never edit** the SRS, `CLAUDE.md`, `.claude/`, or `docs/standards/` without the Approver's instruction. — Stack 18; SRS Section 1.3
9. **No secrets** in code, commits, logs, or AI prompts. — Stack 10.8; AI Policy 11; Change Management Standard 22
10. **Change a governing document only in `deltra-standards`,** never in an application's copy. — Stack 6

## 5. Where to find answers

| Question | Look in |
|---|---|
| What does this application have to do? | The application's SRS, `docs/requirements/` |
| How do I start a new application? | Stack 3; the template's `README.md`, "Start a new application" |
| How do I start work on an existing application, and which commands do I run? | The application's `CLAUDE.md`; the template's `README.md` |
| How do I use Git day to day: branches, commits, pushes, merges? | Change Management Standard 134C |
| How do I name a branch or write a commit message? | Change Management Standard 15, 19–20 |
| Where does a new file or folder go? | Stack 6 |
| Which libraries, versions, and tools may I use? | Stack 4, 5 |
| How is a page built, and how do I add one? | Stack 8; Shell Architecture Document 11 |
| How do database tables, migrations, and queries work? | Stack 9 |
| What does security require? | Stack 10, 12 |
| What testing is required, and what does `make check` do? | Stack 13 |
| What may an agent do, and when must it stop? | AI Policy 26A.5, 26A.6 |
| How do I record a decision? | AI Policy 26A.4; `docs/decisions/decision-log.md` |
| How do I handle a reviewer finding? | Change Management Standard 42, 43 |
| What must be true before merging? | Change Management Standard 48 |
| What goes in a milestone's evidence package? | Change Management Standard 134B; `docs/evidence/TEMPLATE.md` |
| How is a release deployed or rolled back? | The application's `docs/operations/runbook.md`; Change Management Standard 82 |
| Why is the factory built this way? | The factory analogy document |

## 6. What to read, and when

**A new developer, first day (about 30 pages):** this page; the application's `CLAUDE.md`; Stack Sections 2, 6–10, 13, and 18; AI Policy Section 26A; and Change Management Standard Section 134C.

**A new developer, first week:** the Shell Architecture Document; Change Management Standard Sections 39, 42–48, and 134–134B; and the application's SRS.

**Read once and acknowledge, then use as reference:** the full AI Development and Acceptable Use Policy and the full Source Control, Code Review, and Change Management Standard. They are company policies; a developer signs an acknowledgment of them rather than studying every section.

**Agents** need none of this list: the application's `CLAUDE.md` and the Reviewer Agent definitions point them to exactly what they need.

## 7. If you are the Approver

Your work happens at three points, and the Change Management Standard gives a checklist for each: what you receive, what you check, what you decide, and how you record it (Section 134A).

- **Requirements Gate:** approve the SRS before building starts.
- **Acceptance Gate:** at the end of each milestone, test the work yourself, ratify or change the agents' Assumed decisions, and accept the milestone or send it back.
- **Release Gate:** approve a release, tag it, and deploy it by the runbook.

Between gates, you answer only the required stops (AI Policy 26A.5). Some actions are yours alone: the Git actions listed in Change Management Standard 134C, changes to `CLAUDE.md`, `.claude/`, and the pinned standards (Section 44), and the other responsibilities in AI Policy 81.6.

## 8. Glossary

### Factory terms

Each term is defined in the section cited; the line here is a reminder, not the definition.

| Term | Meaning | Defined in |
|---|---|---|
| Approver | The accountable human who holds every approval; currently the CEO | AI Policy 26A.1 |
| Builder Agent, Reviewer Agent | The agent that builds; the read-only agents that check its work | AI Policy 26A.1–26A.2 |
| Development Mode, Production Mode | Synthetic data and agent work, versus real data and Approver-only work | AI Policy 26A.3 |
| Synthetic data, synthetic user | Made-up records and made-up people, used in place of real ones during development; `make synth` creates them | AI Policy 26A.7 |
| Assumed decision, decision log | A business decision an agent made and recorded for later ratification; the file where these are kept | AI Policy 26A.4 |
| Required stop | A situation in which an agent must stop and ask the Approver | AI Policy 26A.5 |
| Quality gate | One automated check that every change must pass; `make check` runs them all | Stack 13.2 |
| Requirements, Acceptance, and Release Gates | The three points where the Approver decides whether work goes forward | Change Management Standard 134A |
| Milestone | A testable piece of the application, accepted at one Acceptance Gate | Change Management Standard 134A; SRS Section 11 |
| Evidence package | The proof the Builder assembles so the Approver can decide without reading code | Change Management Standard 134B |
| Finding, verdict, sign-off | A reviewer's rated problem (High, Medium, Low); its overall result (PASS, PASS WITH FINDINGS, FAIL); a passing verdict on the change as merged | Change Management Standard 42, 44 |
| Critical change | A change to agent instructions, permissions, quality gates, or the pinned standards; only the Approver makes it | Change Management Standard 44 |
| Single-approver operation | How the factory runs with one human, and the controls that compensate | Change Management Standard 39 |
| Reference template | The working implementation of the Stack, copied for every new application | Stack 3 |
| ADR | Architecture Decision Record: a short written record of a design decision and why it was made | Stack 2 |
| Fixed plant, tooling, retooling | What stays the same for every application; what changes per application; adapting the factory to a new one | This page, Section 1 |
| Mock Acceptance Gate | A practice Acceptance Gate, used only in the first-week exercise | This page, Section 9 |

### General terms

| Term | Meaning |
|---|---|
| Git | The version-control system that records every change to the code and who made it |
| Repository | A project's folder of files together with its full Git history |
| Commit | One recorded snapshot of changes, with a message saying what it accomplished |
| Branch | A separate line of work, so changes are made away from `main` until approved |
| `main` | The repository's principal branch; in the factory it always holds release-ready code |
| Push, pull | Send your commits to GitHub; bring others' commits down from GitHub |
| GitHub | The service that hosts Deltra's repositories, Pull Requests, and automated checks |
| Pull Request | A request on GitHub to merge a branch into `main`, showing the changes, check results, and discussion |
| Merge, squash merge | Bring a branch's changes into `main`; a squash merge combines all of a branch's commits into one |
| Submodule | A repository included inside another at a pinned version; `docs/standards/` is one |
| CI (continuous integration) | Automated checks that GitHub runs on every Pull Request |
| `make`, `Makefile` | A tool that runs named recipes of commands; the `Makefile` is the menu, so `make check` runs the recipe called *check* |
| Migration | A numbered SQL file that changes the database structure one step at a time |
| Query, sqlc | The SQL the application runs; sqlc turns that SQL into Go code |
| Claude Code | Anthropic's AI coding agent, used in the terminal; the factory's only development agent |
| Subagent | A Claude Code agent with its own instructions and context, called by the main session; the Reviewer Agents are subagents |

## 9. Learn by doing: the first-week exercise

The exercise teaches the path every change travels. In daily work, Claude Code does most of the typing; the purpose here is to understand what it does.

1. **See the template working.** Clone the template, then run:
   - `make db-dev` — create an empty practice database on your machine;
   - `make synth` — fill it with synthetic users and about 600 synthetic items;
   - `make run` — start the application, then open http://localhost:8080.

   Sign in by choosing a synthetic user from the list. Use every page: the dashboard; the items list with search, filters, sorting, and paging; create an item, edit it, and read its history; leave the title empty to see an error message; open the audit log. Sign out, sign in as a *tech* user, and notice that the Admin menu is gone.

2. **See the quality gates.** Run `make check`. Read the table in Stack Section 13.2, which lists each check it runs and what each catches: formatting, code analysis, known vulnerabilities, secrets, generated code, database migrations, and tests.

3. **Make one change through every layer.** Create a branch as Change Management Standard 134C describes, for example `feature/item-priority`. Add a *priority* (Low, Normal, or High) to the template's example items:

   | Layer | What changes | Where |
   |---|---|---|
   | Migration | A new `priority` column on the items table | `db/migrations/` |
   | Query | The SQL that saves and reads items includes priority; then run `make generate` | `db/queries/items.sql` |
   | Validation | Only Low, Normal, or High is accepted | `internal/items/validate.go` |
   | Page | A drop-down on the form; priority shown on the list and detail pages | `web/templates/pages/` |
   | Test | A test proving priority saves, and that a bad value is rejected | the `_test.go` files |
   | Data dictionary | A description of the new column | `docs/database/data-dictionary.md` |

   Commit each step as it works, and run `make check` until it passes.

4. **Take it through review.** Push the branch and open a Pull Request (Change Management Standard 134C). Ask Claude Code to run the Reviewer Agents that `CLAUDE.md` requires for this change: `srs-reviewer`, `db-safety-reviewer` (a migration changed), and `security-reviewer` (form handling changed). Read each report, fix every High and Medium finding, and run the reviewer again (Change Management Standard 42–43).

5. **Hold a mock Acceptance Gate.** Assemble a short evidence package from `docs/evidence/TEMPLATE.md`: what changed in plain language, proof that `make check` passed, the reviewer reports and how each finding was handled, any decision you made yourself (recorded as Assumed), and step-by-step instructions for trying the change. Present it to the Approver, who follows the Acceptance Gate checklist (Change Management Standard 134A) and accepts the change or asks for rework.

After this, the documents describe things you have already done.

---
*End of Document*
