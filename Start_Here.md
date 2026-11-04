# Start Here — the Deltra Software Factory

| Attribute | Value |
|---|---|
| Version | 1.0 — Draft, pending approval |
| Date | 2026-11-04 |
| Owner | Deltra Systems LLC — Software Development |
| Audience | Anyone who builds, reviews, or approves Deltra web applications |

> **CONFIDENTIAL — Deltra Systems LLC. Internal engineering reference.**

This page is a guide, not a rulebook. It restates nothing: each rule and answer below points to the document and section that governs it. Where this page and a governing document disagree, the governing document wins.

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
Pull Request ─► CI runs make check ─► Reviewer Agents report ─► merge on sign-off
   │
   ▼
Evidence package ─► Acceptance Gate (Approver tests and ratifies) ─► … next milestone
   │
   ▼
Release Gate ─► Approver deploys by the runbook
```

Workflow: Change Management Standard 134. Gates: 134A. Evidence package: 134B.

## 4. Rules that are never broken

1. **Synthetic data only in development.** Real data appears only in production. — AI Policy 26A.7
2. **No production access from a development machine.** Agents and developers never hold production credentials; only the Approver deploys. — AI Policy 26A.9; Stack 15
3. **Stop and ask the Approver** before anything on the required-stops list. — AI Policy 26A.5
4. **Open business decisions don't stop work:** decide, record as Assumed, continue. Never decide silently. — AI Policy 26A.4
5. **Nothing merges** without a passing `make check` and sign-off from each required Reviewer Agent. — Change Management Standard 44, 48
6. **Every change cites the requirement IDs it serves, and every requirement has a test.** — SRS Section 1.3
7. **Part A of the Stack is not optional.** A needed deviation is proposed in an ADR and decided by the Approver. — Stack 2
8. **Never edit** the SRS, `CLAUDE.md`, `.claude/`, or `docs/standards/` without the Approver's instruction. — Stack 18; SRS Section 1.3
9. **No secrets** in code, commits, logs, or AI prompts. — Stack 10.8; AI Policy 11
10. **Change a governing document only in `deltra-standards`,** never in an application's copy. — Stack 6

## 5. Where to find answers

| Question | Look in |
|---|---|
| What does this application have to do? | The application's SRS, `docs/requirements/` |
| How do I start, and which commands do I run? | The application's `CLAUDE.md`; the template's `README.md` |
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

**A new developer, first day (about 30 pages):** this page; the application's `CLAUDE.md`; Stack Sections 2, 6–10, 13, and 18; and AI Policy Section 26A.

**A new developer, first week:** the Shell Architecture Document; Change Management Standard Sections 39, 42–48, and 134–134B; and the application's SRS.

**Read once and acknowledge, then use as reference:** the full AI Development and Acceptable Use Policy and the full Source Control, Code Review, and Change Management Standard. They are company policies; a developer signs an acknowledgment of them rather than studying every section.

**Agents** need none of this list: the application's `CLAUDE.md` and the Reviewer Agent definitions point them to exactly what they need.

## 7. Learn by doing: the first-week exercise

1. Clone the template, then run `make db-dev`, `make synth`, and `make run`. Sign in as a synthetic user and use every page.
2. Run `make check` and read what each gate does (Stack 13.2).
3. On a branch, add one field to the template's example "items", through every layer: migration, query, validation, page, test, and data dictionary.
4. Open a Pull Request, call each Reviewer Agent the change requires, and resolve the findings.
5. Assemble a short evidence package and present it to the Approver as a mock Acceptance Gate.

After this, the documents describe things you have already done.

---
*End of Document*
