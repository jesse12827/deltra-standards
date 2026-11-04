# Web-App Development Stack

## Document Record

| Attribute | Value |
|---|---|
| File name | `Web-App_Development_Stack.md` |
| Version | 2.0 |
| Date | 2026-11-03 |
| Owner | Deltra Systems LLC — Software Development |
| Scope | Deltra web application development standard. Part A is the mandatory baseline for new applications; Part B governs existing applications. Not a record of any one application's deployed state. |
| Canonical location | The `deltra-standards` repository holds the master. Each adopting project includes that repository as a Git submodule at `docs/standards/`, pinned to the version in force (Section 6). |
| Status | Draft — pending approval. Version 1.7 remains the released version until this version is approved. |
| Approved by | Pending |
| Approval date | Pending |
| Release date | Pending |
| Related documents | Deltra AI Development and Acceptable Use Policy (v1.1); Deltra Source Control, Code Review, and Change Management Standard (v1.1); Software Architecture Document — Web Application Shell (v2.0); Deltra Secure Software Development Standard |

## Document History

| Version | Date | Change | Approval |
|---|---|---|---|
| 1.4 | 2026-10-20 | Previous working draft supplied for review | Pending |
| 1.5 | 2026-10-20 | Separate general standards from application-specific implementation; align document responsibilities and paths | Pending |
| 1.6 | 2026-10-23 | Clarify the distinction between verified tool integration and cross-project standards; retain setup details in dedicated guidance | Pending |
| 1.7 | 2026-11-02 | Remove project-specific adoption section and named project instruction-file references; permit either OpenAI Codex or Claude Code; generalize deployment and database qualifications; approved for release | Approved by Jesse Garcia, CEO, 2026-11-02 |
| 1.8 | 2026-11-03 | Standardize on Claude Code as the sole AI engineering agent (draft; superseded by 2.0 before approval) | Not submitted |
| 2.0 | 2026-11-03 | Full rewrite for the AI software factory. Replaces preferences and options with decisions; adds project layout, architecture, frontend, database, security, configuration, logging, testing, quality gates, build, deployment, environments, and agent-repository requirements; separates the mandatory baseline for new applications (Part A) from the adoption policy for existing applications (Part B); lists standard decisions for Approver confirmation (Appendix A). Revised before approval: all SQL consolidated in `db/` (bootstrap, migrations, queries, scripts) with database documentation in `docs/database/`; single `docs/` tree with the runbook moved to `docs/operations/`; `deploy/` limited to installed files; README required in each top-level directory; synthetic data generator moved to `cmd/synthdata`; Go code confined to `cmd/` and `internal/`. Removed agent session capture (Entire), which was optional in Version 1.7, from the standard. Further revised before approval: added `docs/standards/` for read-only copies of the governing documents (Sections 6 and 19, document record); replaced the single `reviewer.md` with one or more Reviewer Agent definitions named by the application's SRS, and required `CLAUDE.md` to point to Section 6 for the layout, listing only the application's own additions, and to give reviewer instructions (Sections 6 and 18); added a `reviews/` folder to each milestone's evidence package and a file name for each reviewer report (Sections 6 and 18); required sign-off from each Reviewer Agent a change requires (Section 17). Revised again before approval with Approver decisions of 2026-11-04 (Appendix A, SD-11 to SD-17): `docs/standards/` is a Git submodule of `deltra-standards` rather than copies (Sections 6, 13.2, 19); the application shell follows the Software Architecture Document — Web Application Shell, with htmx history caching off, active navigation marked by JavaScript, and the sidebar preference kept in browser storage (Section 8); the shell script is a classic script, other scripts ES modules (Section 8); Alpine.js may be permitted only by ADR, in its CSP build (Section 8); every SRS follows the standard section structure (Section 19); Reviewer Agents are application-neutral, read-only, use a different Claude model from the Builder Agent, and report High, Medium, and Low findings with a PASS, PASS WITH FINDINGS, or FAIL verdict (Section 18) | Pending |

> **CONFIDENTIAL — Deltra Systems LLC. Internal engineering reference.**

## 1. Purpose and scope

This standard defines how Deltra web applications are built. Its first audience is the AI agents that build them, and its second is the Approver who accepts their work.

A software factory builds many applications *consistently*. For that reason, Part A states decisions, not preferences. Where Version 1.7 said "when suitable" or "optional," this version states what is used.

- **Part A** is the mandatory baseline for every new Deltra web application.
- **Part B** governs existing applications, which are not forced to rewrite.

Roles and terms used here — **Approver**, **Builder Agent**, **Reviewer Agent**, **Development Mode**, and **Production Mode** — have the meanings given in Section 26A of the Deltra AI Development and Acceptable Use Policy.

## 2. How to apply this standard

- **"Shall"** is mandatory. **"Should"** is expected unless an Architecture Decision Record (ADR) explains why not.
- A Builder Agent shall not deviate from a "shall" requirement. A deviation contradicts an approved standard, which is a required stop under Section 26A.5 of the AI Policy. The agent records the proposed deviation and the Approver decides.
- An application's approved SRS may set stricter or more specific values than this standard, such as session lifetimes or accessibility targets. Where it does, the SRS governs for that application.
- Changes to this standard follow the Change Management Standard and require Approver approval.

## 3. Reference template repository

The **reference template repository** (`deltra-webapp-template`) is the working implementation of Part A. It contains the project layout, middleware, session handling, database access pattern, migration setup, test harness, quality gates, Makefile, systemd unit, Nginx configuration, and agent files described below.

- Every new application shall start as a copy of the current template.
- Agents shall follow the template's code as the pattern for anything it demonstrates.
- Where the template and this standard disagree, this standard governs, and the template shall be corrected.

---

# Part A — Baseline for New Applications

## 4. Platform

| Concern | Standard | Version policy |
|---|---|---|
| Operating system | Debian 13 | Current Debian stable; security updates applied per Deltra patching practice |
| Language | Go | Go 1.27 for new projects. The `go` and `toolchain` directives in `go.mod` pin the version. Adopt each new Go release within 90 days of its first point release (for example, 1.28.1). |
| Database | PostgreSQL | PostgreSQL 18, installed from the PostgreSQL Global Development Group (PGDG) apt repository. A new major version may be adopted after its first minor release (for example, 19.1) with Approver approval. |
| Reverse proxy | Nginx | Debian package |
| Process supervision | systemd | Debian package |
| Source control | Git | One repository per application |

## 5. Dependencies

The Go standard library is used first. The following third-party components are approved for new applications:

| Purpose | Component | Notes |
|---|---|---|
| PostgreSQL driver and pool | `github.com/jackc/pgx/v5` | `pgxpool` for connection pooling |
| Query code generation | `sqlc` (build-time tool) | Generates type-safe Go from SQL; uses the pgx/v5 driver |
| Schema migrations | `github.com/pressly/goose/v3` | SQL migrations only |
| Password hashing | `golang.org/x/crypto/argon2` | Only for applications with local accounts (Section 10.1) |
| OpenID Connect | `github.com/coreos/go-oidc/v3` and `golang.org/x/oauth2` | For Microsoft Entra ID sign-in |
| Browser interactivity | htmx 2.0.x | Vendored file; see Section 8 |

Development tools used by the quality gates (Section 13) are approved as tools, not as application dependencies.

Any other dependency requires an ADR stating why the standard library and approved components are insufficient, and its license. New dependencies are listed in the milestone evidence package.

Dependencies are pinned through `go.mod` and `go.sum`. Vendored frontend files are pinned by version in their filename.

## 6. Project layout

Every new application shall use this layout:

```text
<app>/
├── README.md                      Purpose, local build and run, links to docs/
├── CLAUDE.md                      Agent instructions (Section 18)
├── Makefile                       Single source of build, test, and check commands
├── go.mod / go.sum                Go module definition
├── sqlc.yaml                      sqlc configuration
├── .golangci.yml                  Lint configuration
├── .claude/
│   ├── settings.json              Agent permission rules
│   └── agents/
│       └── *.md                   Reviewer Agent definitions (Section 18)
│
├── cmd/                           GO CODE: entry points
│   ├── <app>/
│   │   └── main.go                Application; subcommands: serve, migrate, version
│   └── synthdata/
│       └── main.go                Synthetic data generator (never deployed)
├── internal/                      GO CODE: application packages
│   ├── config/                    Configuration loading and validation
│   ├── dbgen/                     Code generated by sqlc from db/queries (do not edit)
│   ├── web/                       Router, handlers, middleware, template rendering
│   ├── auth/                      Sessions, sign-in, authorization checks
│   ├── audit/                     Audit event recording
│   ├── synth/                     Synthetic data definitions used by cmd/synthdata and tests
│   └── <domain>/                  One package per business area (e.g., procurement)
│
├── db/                            ALL SQL
│   ├── README.md
│   ├── embed.go                   Embeds migrations into the binary
│   ├── bootstrap/                 Creates the database and roles; run once per server
│   ├── migrations/                Numbered schema changes (goose)
│   ├── queries/                   Application queries (compiled by sqlc)
│   └── scripts/                   Operational SQL: backup checks, maintenance
├── web/                           USER INTERFACE FILES
│   ├── README.md
│   ├── embed.go                   Embeds templates and static files into the binary
│   ├── templates/                 html/template files (layouts, pages, partials)
│   └── static/
│       ├── css/
│       ├── js/
│       └── vendor/                htmx-2.0.x.min.js
├── deploy/                        DEPLOYED CONFIGURATION ONLY
│   ├── README.md
│   ├── <app>.service              systemd unit
│   └── nginx-<app>.conf           Nginx site template
│
└── docs/                          ALL DOCUMENTATION
    ├── requirements/              Approved SRS
    ├── standards/                 Git submodule: deltra-standards at the version in force
    ├── database/                  Data dictionary and schema diagram
    ├── operations/
    │   └── runbook.md             Deployment, rollback, backup, and restore
    ├── adr/                       Architecture Decision Records
    ├── decisions/
    │   └── decision-log.md        Agent decision log (AI Policy 26A.4)
    ├── evidence/                  Milestone evidence packages, one folder per milestone
    │   └── <milestone>/
    │       └── reviews/           Reviewer Agent reports
    └── user-guide.md
```

Layout rules:

- **Go code lives only in `cmd/` and `internal/`.** The sole exceptions are `db/embed.go` and `web/embed.go`, which exist because Go's `embed` feature can include only files in or below the directory of the Go file that embeds them.
- **All SQL lives in `db/`.** No SQL is written inside Go source files.
- **All documentation lives in `docs/`.** Other top-level directories contain a short `README.md` that says what belongs in them, and nothing more.
- **`deploy/` contains only files that are installed on a server.**
- **`docs/standards/` is a Git submodule** of the `deltra-standards` repository, pinned to the commit in force for the application. It gives agents the governing documents inside the repository, where they can read them, while each document keeps a single master. Agents never edit it. Only the Approver moves the pinned commit, as a Critical change, and the move is recorded in the decision log or an ADR.
- Test fixtures specific to one package go in a `testdata/` directory inside that package, following Go convention.

## 7. Application architecture

- **HTTP routing:** the standard library `net/http.ServeMux`, using method and path patterns. No third-party router.
- **Rendering:** server-rendered HTML with `html/template`. Full pages use a base layout. htmx requests receive partial templates.
- **Layers:** handlers in `internal/web` parse and validate input, call domain packages, and render. Domain packages hold business rules. Domain packages access the database only through the sqlc-generated code in `internal/dbgen`. Handlers do not contain SQL.
- **No ORM.** SQL is written by hand and compiled by sqlc.
- **No JSON API** unless the application's SRS requires one. If it does, it uses the same authentication, authorization, and validation as the HTML interface.
- **Context and timeouts:** every request has a context with a deadline. Database calls use the request context. The server sets read, write, and idle timeouts.
- **Graceful shutdown** on SIGTERM, allowing in-flight requests to finish.

## 8. Frontend

The application shell — navigation, page layout, device tiers, focus handling, error display, and htmx behavior — is defined in the **Software Architecture Document — Web Application Shell** in `deltra-standards`, and implemented in the reference template. This section states the rules; that document explains the design.

- **HTML first.** Pages work as plain HTML forms and links. htmx enhances them: the navigation swaps only the main view (`#main-view`), and lists may swap a results region.
- **htmx 2.0.x**, vendored under `web/static/vendor/` with its version in the file name, and served by the application. No CDN.
- **htmx configuration** is declared once, in the layout's `htmx-config` meta element: history caching off (`historyCacheSize: 0`), so pages showing client data are never copied into browser storage; indicator styles off and `eval` off, for the Content Security Policy.
- **No JavaScript build step and no npm.** Custom JavaScript is vanilla, in `web/static/js/`. The shell script (`spa-shell.js`) is a classic script loaded in the page head, so it applies the sidebar state before the page appears; page-specific scripts are ES modules. No inline scripts or inline event handlers, so the Content Security Policy can forbid them (Section 10.6).
- **Active navigation is marked by JavaScript**, by one rule: the navigation link whose address is the longest prefix of the current address is the active page. The server does not mark it.
- **The sidebar preference is kept in browser storage** (`localStorage`). It is a per-browser convenience; failure to store it is never an error. It is not stored in the database.
- **Alpine.js is not used** in new applications. Small interactive behavior is written in vanilla JavaScript. If an application genuinely needs rich in-browser state, an ADR approved by the Approver may permit Alpine's CSP build for that application only.
- **Partial responses:** a handler that returns part of a page for an htmx request sends `Vary: HX-Request`. htmx 2 does not swap error responses, so forms that report validation errors (HTTP 422) are submitted as ordinary forms unless htmx is configured to swap those responses.
- **CSS:** vanilla CSS with design tokens as custom properties. CSS Grid for page layout. Container queries for components. Media queries only for the page shell and device-level concerns. No CSS framework.
- **Accessibility:** WCAG 2.2 Level AA is the default target. Every form control has a label. Every function is usable by keyboard. Errors are announced next to the field they concern.
- **Browsers:** current versions of Microsoft Edge, Google Chrome, Mozilla Firefox, and Safari.

## 9. Database

### 9.1 Conventions

- Each application uses its own database and a schema named for the application, not `public`.
- Names are `snake_case`. Table names are plural.
- Primary keys are `bigint GENERATED ALWAYS AS IDENTITY`.
- Points in time use `timestamptz`. Calendar dates use `date`. Date-only values are never stored as timestamps.
- Text uses `text`, with `CHECK` constraints for length or format where needed.
- Every relationship has a foreign key.
- Business tables carry provenance columns: `created_at`, `created_by`, `updated_at`, `updated_by`.
- Reference data that the application depends on, such as status lists, is created by migrations.

### 9.2 Roles and connections

Each application has two database roles:

| Role | Purpose | Privileges |
|---|---|---|
| `<app>_owner` | Owns the schema and its objects; runs migrations | Object ownership; used only by the Approver in production |
| `<app>_app` | The running application | `CONNECT`, `USAGE` on the schema, and only the table and column privileges the application needs. No DDL. |

- The application connects through the local Unix socket. TCP connections, where required, use `scram-sha-256`.
- No application connects as a superuser.
- Row-level security is used when the SRS requires separation of rows between users or tenants.

### 9.3 Bootstrap

- `db/bootstrap/` contains the SQL that creates the application's database, schema, and two roles. It must run as the PostgreSQL administrator before any migration, which is why it is not a migration.
- Bootstrap scripts are idempotent: running them again changes nothing.
- They contain no passwords. Role authentication is configured per Section 9.2.
- In Development Mode, agents run bootstrap scripts against disposable development servers. In production, the Approver runs them once per server, as described in the runbook.

### 9.4 Migrations

A migration is a numbered SQL script that changes the database structure by one step. Migrations run in order, and the database records which have run, so every environment reaches the same structure.

- Migrations are goose SQL files in `db/migrations/`, numbered sequentially, each with `Up` and `Down` sections.
- Schema migrations and synthetic development data are separate. Synthetic data never appears in `db/migrations/`.
- Every migration is tested up, down, and up again in the quality gates (Section 13).
- In Development Mode, agents run migrations against disposable databases.
- In production, the Approver runs migrations with the `<app>_owner` role, using the `migrate` subcommand, as described in the runbook.
- Destructive migrations follow Sections 74–75 of the Change Management Standard.

### 9.5 Queries

- Application queries live in `db/queries/` and are compiled by sqlc into Go code in `internal/dbgen/`. Generated code is never edited by hand.
- All values are passed as parameters. String concatenation of SQL with user input is prohibited.
- Lists and searches are paginated in SQL. No query loads an unbounded result set.
- Multi-table changes run in a single transaction.

### 9.6 Operational scripts and database documentation

- SQL the Approver runs for operations, such as backup verification and maintenance, lives in `db/scripts/`. Each script begins with a comment stating its purpose, the role it runs as, and whether it changes data.
- The data dictionary and schema diagram live in `docs/database/` and are updated in the same Pull Request as any migration that changes them.

## 10. Security baseline

These requirements apply to every new application, in addition to the Deltra Secure Software Development Standard.

### 10.1 Authentication

- **Staff sign-in uses Microsoft Entra ID through OpenID Connect**, either directly or through Deltra's shared Entra ID authentication service.
- **Local accounts** are used only when the application's SRS requires them. Passwords are hashed with Argon2id using parameters set in the template. Password reset is an administrator-controlled process unless the SRS specifies self-service.
- Authentication failures are logged as audit events without the submitted password.

### 10.2 Sessions

- Sessions are stored server-side in PostgreSQL.
- The session token is 32 bytes from a cryptographically secure random source. The database stores only its SHA-256 hash.
- The cookie uses the `__Host-` prefix and the `Secure`, `HttpOnly`, and `SameSite=Lax` attributes.
- The session token is replaced at sign-in. Sign-out deletes the session on the server.
- Default lifetimes are 30 minutes idle and 8 hours absolute, unless the SRS sets other values.

### 10.3 Authorization

- Authorization is enforced on the server for every request. Hiding a link or button is not authorization.
- Access is denied by default. Each route declares the role or permission it requires.
- Record-level checks confirm that the user may act on the specific record identified in the request.

### 10.4 Cross-site request forgery

- All state-changing requests use POST (or PUT, PATCH, DELETE). GET requests never change state.
- The server applies the standard library's `http.CrossOriginProtection` to all routes, in addition to `SameSite=Lax` cookies.

### 10.5 Input and output

- All input is validated on the server for type, length, format, and referenced-record existence. Validation errors are returned per field without losing the user's input.
- Output is escaped by `html/template`. Converting user-supplied content to `template.HTML` is prohibited.

### 10.6 Response headers

The application or Nginx sets:

- `Content-Security-Policy: default-src 'self'; script-src 'self'; style-src 'self'; frame-ancestors 'none'; form-action 'self'; base-uri 'none'`
- `X-Content-Type-Options: nosniff`
- `Referrer-Policy: same-origin`
- `Strict-Transport-Security` (set by Nginx)

### 10.7 Errors

- Users see a generic message and a request identifier.
- SQL text, stack traces, file paths, credentials, and infrastructure details are never shown to users. They are logged with the request identifier.

### 10.8 Secrets

- Secrets are never stored in source control, logs, templates, or agent prompts.
- In production, secrets are supplied as systemd credentials (`LoadCredentialEncrypted=`) and read from `$CREDENTIALS_DIRECTORY`.
- In Development Mode, secrets are development-only values held outside the repository.

## 11. Configuration

- Non-secret configuration is supplied through environment variables, set in the systemd unit or a referenced environment file.
- `internal/config` loads and validates all configuration at startup. The application refuses to start with missing or invalid configuration and names the problem in its log.
- Configuration has no hidden defaults for security-relevant values.

## 12. Logging and audit

- Logs use the standard library `log/slog` in JSON format, written to standard output and collected by journald.
- Every log entry for a request carries the request identifier.
- Logs never contain passwords, session tokens, credentials, or full request bodies.
- **Audit events** are written to an audit table in the application database and to the log. Audit events include sign-in success and failure, authorization denials, user and role administration, and creation, change, and deletion of business records.
- Audit records are never updated or deleted by the application.

## 13. Testing and quality gates

### 13.1 Testing

- Tests use the standard `go test` tool.
- **Integration tests run against real PostgreSQL 18.** Each test run creates a disposable database, applies all migrations, and loads synthetic fixtures. The database is not mocked.
- **Acceptance tests** are Go tests named for the SRS acceptance test they implement (for example, `TestAT01_LoginLogout`). They exercise the application through HTTP using `net/http/httptest`.
- **Negative tests are required** for authentication, authorization, CSRF protection, and input validation.
- Every SRS requirement maps to at least one test or to an acceptance test the Approver performs personally. The mapping is recorded in the evidence package.
- **Synthetic data** is produced by `cmd/synthdata` (using `internal/synth`) from a fixed random seed, so results are repeatable. It never contains real client, staff, or production data.

### 13.2 Quality gates

`make check` runs every gate. Continuous integration runs `make check` on every Pull Request. All gates must pass before merge (Change Management Standard, Section 48).

| Gate | Tool |
|---|---|
| Formatting | `gofmt` (no unformatted files) |
| Static analysis and lint | `go vet` and `golangci-lint`, including `staticcheck`, `errcheck`, and `gosec`, as configured in the template |
| Known vulnerabilities | `govulncheck` |
| Secret detection | `gitleaks` |
| Generated code current | `sqlc diff` |
| Migrations | Up, down, and up on a disposable database |
| Tests | `go test -race ./...` |

Continuous integration checks out the `docs/standards/` submodule and runs every gate independently of any agent. This is the independent test run behind each merge: Reviewer Agents are read-only and do not run the gates themselves (Section 18).

Changes to the Makefile gate targets, `.golangci.yml`, or CI definitions are Critical changes under Section 44 of the Change Management Standard.

## 14. Build and release

- `make build` produces a single static binary: `CGO_ENABLED=0`, `-trimpath`, with the release version and commit embedded through `-ldflags`.
- The binary contains its templates, static files, and migrations.
- The binary reports its version through the `version` subcommand and in its startup log entry.
- Releases are tagged `vMAJOR.MINOR.PATCH` (semantic versioning). Only the Approver creates release tags.
- A release consists of the binary, its SHA-256 checksum, release notes, and the milestone evidence package.

## 15. Deployment

- The application runs as a dedicated system user with no login shell.
- Releases are installed under `/opt/<app>/releases/<version>/`, with `/opt/<app>/current` as a symbolic link to the active release. Application rollback switches the link to the previous release and restarts the service. Database rollback follows the migration plan in the runbook.
- The systemd unit includes, at minimum: `NoNewPrivileges=yes`, `ProtectSystem=strict`, `ProtectHome=yes`, `PrivateTmp=yes`, `PrivateDevices=yes`, an empty `CapabilityBoundingSet=`, and `RestrictAddressFamilies=AF_UNIX AF_INET AF_INET6`.
- The application listens on `127.0.0.1` or a Unix socket. Nginx terminates TLS and forwards requests.
- Nginx sets TLS 1.2 as the minimum (TLS 1.3 preferred), HSTS, request body size limits, and timeouts.
- The application provides `/healthz`, which reports process health and database connectivity, for monitoring.
- **Production deployment is performed by the Approver** by following `docs/operations/runbook.md`. Agents prepare the runbook and never deploy.

## 16. Environments

| Environment | Who works in it | Data | Purpose |
|---|---|---|---|
| Development | Builder and Reviewer Agents | Synthetic only | Building and automated testing; databases are disposable |
| Acceptance | Approver | Synthetic only | Approver acceptance testing of a release candidate |
| Production | Approver only | Real | Live operation |

Rehearsal of a real-data migration, such as importing records from an existing application, is Production Mode work. The Approver performs it in an isolated environment that agents cannot access.

## 17. Source control

- Git, with one repository per application, hosted in a Deltra-controlled GitHub organization with private repositories.
- Branching, commit messages, Pull Requests, and merge rules follow the Change Management Standard.
- `main` is protected and requires passing gates and sign-off from each Reviewer Agent the change requires (Section 18).

## 18. AI engineering agent

**Claude Code (Anthropic) is the sole AI engineering agent** for the Builder and Reviewer Agent roles. Its use is governed by Section 26A of the AI Policy. The Claude Code account type used must be recorded in the approved AI services inventory before use.

Every repository shall contain:

| File | Purpose |
|---|---|
| `CLAUDE.md` | Short project instructions: the application's purpose; the model each agent role uses; links to its SRS, this standard, and the governing policies; a pointer to Section 6 for the project layout, listing only the application's own additions to it (such as its domain packages); the `make` commands; the synthetic-data rule; the required stops; and when to call each Reviewer Agent, what to give it, and where its findings are saved. It points to other documents rather than repeating them. |
| `.claude/agents/*.md` | The Reviewer Agent definitions. The template provides four: `srs-reviewer`, `security-reviewer`, `db-safety-reviewer`, and `doc-consistency-reviewer`. The application's SRS names the reviewers it requires and when each runs. |
| `.claude/settings.json` | Permission rules that deny agents access to production hosts, production credentials, and secret files. |
| `docs/decisions/decision-log.md` | The agent decision log (AI Policy, Section 26A.4). |

Changes to `CLAUDE.md`, `.claude/settings.json`, `.claude/agents/`, or `docs/standards/` are Critical changes under Section 44 of the Change Management Standard, because they change the instructions, permissions, and standards that govern agents.

Reviewer Agent definitions follow these rules:

- **Application-neutral.** A definition states *how* to check and *which SRS sections* to check against; it does not copy application content such as status lists or scope lists. The application's specifics live only in its SRS, so the same reviewers serve every application.
- **Read-only.** Reviewers have only the Read, Grep, and Glob tools. They report findings and never edit files or run commands.
- **A different model from the Builder Agent** (AI Policy Section 26A.2). The Builder Agent runs on Claude Opus; Reviewer Agents run on Claude Sonnet.
- **One report format.** Findings are rated High, Medium, or Low, and each report ends with a verdict: PASS, PASS WITH FINDINGS, or FAIL. Any High finding is a FAIL (Change Management Standard Section 42).

Each Reviewer Agent report is saved unchanged as `docs/evidence/<milestone>/reviews/<agent>-<YYYY-MM-DD>.md`. How each finding was resolved is recorded in the same evidence package.

## 19. Project documentation

Each application maintains, in its repository:

- `README.md` — purpose, how to build and run locally, and links to the documents below;
- a short `README.md` in each top-level directory other than `docs/`, saying what belongs there;
- the approved SRS in `docs/requirements/`, as one file holding the current version. Every SRS follows the standard section structure, because the Reviewer Agents find what they check by section: 1.3 builder rules, 1.4 scope, 1.5 source precedence, 1.7 agent roles, 3 business rules and reference data, 4 functional requirements, 5 data requirements (5.3 synthetic data), 7 nonfunctional requirements, 8 data migration (when needed), 9 acceptance tests, 10 open decisions and development defaults, and 11 milestone plan;
- the governing documents in `docs/standards/`, as a Git submodule (Section 6);
- ADRs in `docs/adr/`;
- the decision log;
- milestone evidence packages in `docs/evidence/`;
- `docs/database/` — the data dictionary and schema diagram (Section 9.6);
- `docs/operations/runbook.md` — deployment, rollback, recovery, and backup restoration; and
- `docs/user-guide.md` — the user and administrator guide.

---

# Part B — Existing Applications

## 20. Adoption policy for existing applications

Existing Deltra applications were built before this baseline. They are not required to rewrite to meet it.

- **Inventory first.** Before changing an existing application, verify its actual state: the Go and PostgreSQL versions in use, its dependency files, its router, its frontend libraries, and its deployment configuration. Distinguish current behavior from this standard. Do not assert that an application meets this standard without checking.
- **Preserve what works.** Do not replace a functioning router, frontend library (including Alpine.js), or project structure merely to match Part A. Preserve existing Git history.
- **Apply immediately to any change:** Section 10 (security baseline), Section 9.5 (parameterized queries), Section 12 (no secrets in logs), the AI Policy's agent rules, and the synthetic-data rule.
- **Apply where practical to new code:** the remaining Part A conventions, where they fit the existing structure.
- **Migrating** an existing application fully to Part A is a separate work item that requires an ADR and Approver approval.

---

## 21. Engineering principles

- Follow **DRY**: each fact, procedure, or policy has one authoritative home; link rather than duplicate.
- Build the simplest thing that meets the approved requirements. Prefer the standard library.
- Make the smallest coherent change, with tests, and keep the requirement IDs it serves visible.
- Keep secrets and real client data out of commits, logs, and agent prompts.
- Keep schema changes reproducible. Keep migrations, reference data, and synthetic data separate.
- Require explicit Approver approval for destructive database operations, releases, and deployment.

---

## Appendix A — Standard decisions for Approver confirmation

The following choices were made in preparing Version 2.0. Each is recorded here as **Assumed**, following the decision-log practice of AI Policy Section 26A.4. Approval of this version ratifies all of them unless the Approver changes one.

| ID | Decision | Alternatives considered | Rationale |
|---|---|---|---|
| SD-01 | Git hosting in a Deltra-controlled GitHub organization with private repositories | Self-hosted Gitea | Claude Code integrates directly with GitHub Pull Requests and GitHub Actions; no server to maintain |
| SD-02 | Microsoft Entra ID through OpenID Connect for staff sign-in; local accounts only when an SRS requires them | Local accounts as the default | Staff already sign in with Microsoft 365; Deltra has an Entra ID authentication service; removes password storage from most applications |
| SD-03 | htmx 2.0.x | htmx 4.0, released August 28, 2026 | htmx 4 is a recent major release with breaking changes; htmx 2 is mature and remains the default package version. Revisit in a future revision. |
| SD-04 | Alpine.js not used in new applications | Alpine.js permitted for justified components | One fewer library for agents to use inconsistently; Deltra has already retired Alpine.js from its SPA template work |
| SD-05 | sqlc generates query code from hand-written SQL | Hand-written pgx code only | Compile-time checking of SQL against the schema; consistent code across applications |
| SD-06 | goose for SQL migrations | golang-migrate; tern | Widely used, SQL-only migrations, works with embedded files and a `migrate` subcommand |
| SD-07 | Session lifetimes of 30 minutes idle and 8 hours absolute | Longer lifetimes | Conservative default for internal applications handling client information; an SRS may override |
| SD-08 | WCAG 2.2 Level AA as the default accessibility target | No formal target | A defined target makes accessibility testable; an SRS may override |
| SD-09 | PostgreSQL 18 baseline; adopt a new major after its first minor release | Adopt PostgreSQL 19 at general availability | First minor releases correct early defects |
| SD-10 | golangci-lint as the lint runner, including staticcheck and gosec | Run each analyzer separately | One configuration file shared through the template |

The following were decided by the Approver on 2026-11-04 and are recorded here for traceability. They are not Assumed.

| ID | Decision | Alternatives considered | Rationale |
|---|---|---|---|
| SD-11 | `docs/standards/` is a Git submodule of `deltra-standards`, pinned per application | Read-only copies in each repository | One master per document; agents still read the documents inside the repository; no copies to drift |
| SD-12 | Reviewer Agents are application-neutral and cite SRS sections | Reviewers that restate the application's SRS content | Reviewers stay in the plant; retooling for a new application changes only its SRS |
| SD-13 | Reviewer Agents use a different Claude model from the Builder Agent | The same model | Independent review without shared blind spots (AI Policy 26A.2) |
| SD-14 | Findings rated High, Medium, or Low; verdict PASS, PASS WITH FINDINGS, or FAIL | Required, Security, Question, Suggestion, Nit | One vocabulary for agent and human review |
| SD-15 | Alpine.js remains excluded; its CSP build may be permitted per application by ADR | Alpine.js permitted generally | Alpine's standard build needs `unsafe-eval`, which the Content Security Policy forbids; htmx and the shell cover current needs |
| SD-16 | Active navigation marked by JavaScript using the longest-prefix rule | Marked by the server | The shell persists across htmx navigation, so only JavaScript sees every change; one implementation |
| SD-17 | Sidebar preference stored in browser storage | Stored on the user's database record | A screen preference, not a personal one; no database overhead |

---
*End of Document*
