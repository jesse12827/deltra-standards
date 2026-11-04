## Document Metadata

**Document Title:** Deltra Source Control, Code Review, and Change Management Standard  
**Organization:** Deltra Systems LLC  
**Organizational Unit:** Deltra Software Engineering  
**Document Type:** Engineering and Change Management Standard  
**Parent Document:** Deltra Software Engineering Charter  
**Related Documents:**

- Deltra Engineering Principles and Standards
- Deltra AI Development and Acceptable Use Policy
- Deltra Secure Software Development Standard
- Web-App Development Stack

**Document Owner:** Engineering Technical Lead (role vacant; responsibilities held by the Approver — see Section 39)  
**Approval Authority:** Chief Executive Officer / Executive Product Architect  
**Status:** Draft  
**Version:** 1.1

# 1. Purpose

The purpose of this Standard is to establish requirements governing:

- source-code management;
- repository ownership;
- branching;
- commits;
- Pull Requests;
- code review;
- AI-assisted development;
- testing;
- database changes;
- application releases;
- production changes;
- emergency changes;
- rollback;
- release traceability; and
- change documentation.

The objective is to ensure that Deltra can determine for every material software change:

> **What changed?**
>
> **Why did it change?**
>
> **Who created the change?**
>
> **Who reviewed it?**
>
> **How was it tested?**
>
> **Who authorized its deployment?**
>
> **What version was deployed?**
>
> **When was it deployed?**
>
> **How can Deltra recover if the change fails?**

Software change shall be treated as an engineering process rather than merely the act of copying new files into production.

# 2. Scope

This Standard applies to source code and production changes associated with Deltra-developed or Deltra-maintained software, including:

- Go applications;
- C# and .NET applications;
- Python applications;
- HTML, CSS, and JavaScript;
- APIs;
- Windows services;
- Linux services;
- databases;
- database schemas;
- SQL migrations;
- stored procedures;
- scripts;
- container definitions;
- Podman configurations;
- infrastructure-as-code;
- CI/CD definitions;
- AI prompts used in production;
- AI agent definitions;
- AI tool permissions;
- RAG configuration;
- configuration templates;
- deployment scripts;
- shared libraries;
- documentation maintained with source code; and
- other engineering artifacts affecting production operation.

This Standard applies to:

- employees;
- contractors;
- consultants;
- vendors;
- AI agents acting as Builder or Reviewer Agents under Section 26A of the Deltra AI Development and Acceptable Use Policy; and
- other authorized contributors to Deltra software.

The roles **Approver**, **Builder Agent**, and **Reviewer Agent**, and the terms **Development Mode** and **Production Mode**, have the meanings given in Section 26A of the Deltra AI Development and Acceptable Use Policy.

# 3. Governing Principle

The governing principle of this Standard shall be:

> **Every material production change shall be attributable, reviewable, testable, reproducible, and recoverable.**

# 4. Source Control Requirement

Git shall be the standard source-control system for Deltra Software Engineering unless an approved exception exists.

All significant production software shall be stored in a Deltra-controlled Git repository.

Production software shall not exist solely:

- on an engineer’s workstation;
- on a production server;
- in an email attachment;
- in an AI conversation;
- in a shared folder;
- in a personal repository; or
- in an undocumented backup.

Git shall serve as the authoritative history of Deltra-developed software.

# 5. Company Ownership of Repositories

Repositories containing Deltra intellectual property shall be owned and administratively controlled by Deltra Systems.

Repository ownership shall not depend upon an individual employee’s or contractor’s personal account.

Where an external Git hosting provider is used, Deltra shall maintain an organizational account or equivalent company-controlled administrative structure.

# 6. Repository Administrative Control

At least two authorized Deltra individuals should ultimately be capable of administering critical source-control infrastructure as the engineering organization grows.

Administrative control should not depend permanently upon one person.

Administrative privileges shall follow least privilege.

Routine development does not require repository-administrator authority.

# 7. Approved Repository Locations

Production software shall be stored only in approved Deltra-controlled source repositories.

Personal repositories shall not be used as the authoritative location for Deltra software.

Public repositories shall only contain Deltra source code when public release has been intentionally approved.

# 8. Repository Naming

Repository names should be descriptive, concise, and consistent.

For general repositories, Deltra should prefer:

    lowercase-with-hyphens

Examples:

    deltra-ticketing
    dswinmon-agent
    deltra-ai-gateway
    siem-standardization-engine
    customer-portal

Names should identify the product or major component without unnecessary abbreviations.

# 9. Repository Contents

A significant application repository should generally contain:

    README.md

    src/
    tests/
    docs/
    scripts/
    migrations/
    deploy/

as appropriate to the language and project.

Not every repository requires every directory.

The objective is predictable organization rather than forced uniformity.

# 10. Repository README

Each significant repository shall contain a `README.md`.

The README should identify:

- application purpose;
- major architecture;
- primary technology;
- development prerequisites;
- build procedure;
- configuration approach;
- testing procedure;
- deployment documentation;
- repository owner; and
- links to detailed documentation.

A qualified engineer newly assigned to the project should be able to determine how to begin working with the application without relying entirely upon verbal instruction.

# 11. Protected Default Branch

The default production branch shall normally be:

    main

The `main` branch shall be protected against uncontrolled modification.

Direct modification of `main` should normally be prohibited.

Changes should enter `main` through an approved Pull Request workflow.

# 12. Meaning of `main`

The `main` branch shall represent code that is:

> **production-ready or release-ready according to the applicable project requirements.**

Code in `main` shall not knowingly contain unfinished experimental functionality unless such functionality is safely isolated through an approved mechanism such as a feature flag.

# 13. Branching Philosophy

Deltra shall prefer a simple branching model using short-lived branches.

Typical structure:

    main
     │
     ├── feature/<description>
     ├── bugfix/<description>
     └── hotfix/<description>

Examples:

    feature/ticket-email-ingestion
    feature/customer-role-management

    bugfix/duplicate-ticket-creation
    bugfix/tenant-filtering

    hotfix/login-token-validation

# 14. Short-Lived Branches

Feature and bugfix branches should exist only as long as reasonably necessary to complete and review the associated change.

Long-lived divergent branches should be avoided because they increase:

- merge complexity;
- integration risk;
- security drift;
- deployment uncertainty; and
- duplicated development effort.

# 15. Branch Naming

Branch names should communicate purpose.

Preferred patterns include:

    feature/<short-description>
    bugfix/<short-description>
    hotfix/<short-description>
    docs/<short-description>
    refactor/<short-description>
    security/<short-description>

Where a work-item identifier exists, it may be incorporated.

Example:

    feature/TKT-0142-ticket-attachments

# 16. Direct Commits to `main`

Direct commits to `main` shall normally be prohibited.

An exception may be permitted during:

- initial repository creation;
- a documented emergency;
- recovery of damaged source-control infrastructure; or
- another specifically approved condition.

Routine convenience is not sufficient justification.

# 17. Force Pushes

Force pushes to protected branches shall normally be prohibited.

History of production-relevant branches should remain reliable and auditable.

If exceptional repository repair requires rewriting protected history, the action shall be authorized and documented.

# 18. Commit Principle

A commit should represent one understandable logical change where practical.

A commit should allow another engineer to answer:

> **What did this commit accomplish?**

# 19. Commit Messages

Commit messages shall be meaningful.

Poor examples:

    update
    changes
    fix
    stuff
    working
    final
    final2

Preferred examples:

    Add tenant validation to ticket API

    Fix duplicate webhook processing

    Create PostgreSQL migration for ticket status history

    Restrict AI triage agent to draft-ticket permission

# 20. Commit Message Format

Deltra may initially use a simple commit format:

    <imperative description>

Examples:

    Add audit logging for role changes

    Correct PostgreSQL RLS policy for customer users

    Remove deprecated API endpoint

Where additional context is necessary, a commit body may explain:

- reason;
- implementation details;
- known limitations; and
- associated work item.

# 21. Commit Size

Commits should be small enough to understand but large enough to represent meaningful work.

Engineers should avoid combining unrelated changes such as:

    Add monitoring API
    + redesign login page
    + update database indexes
    + change deployment configuration

into a single commit when those changes can reasonably be separated.

# 22. Secrets in Commits

Commits shall not contain:

- passwords;
- API keys;
- private keys;
- authentication tokens;
- active certificates containing private keys;
- database credentials; or
- other production secrets.

Secret scanning should be incorporated where practical.

# 23. Accidental Secret Commit

If an active secret is committed to Git, the engineer shall assume that the secret may have been exposed.

The response shall include, as applicable:

1.  revoke or rotate the secret;
2.  determine the extent of exposure;
3.  remove the secret from active code;
4.  evaluate repository history;
5.  document the event if material; and
6.  verify replacement credentials.

Deleting the secret in a later commit is not sufficient by itself.

# 24. Generated Files

Files generated automatically during compilation, package restoration, testing, or local development should generally not be committed unless there is a specific reason.

Appropriate `.gitignore` rules shall be maintained.

Examples may include:

- compiler output;
- temporary files;
- local caches;
- local IDE state;
- local secrets;
- test artifacts; and
- operating-system metadata.

# 25. Source-Control Documentation

Documentation closely associated with software should normally be maintained in the same source-control environment as the software.

Examples include:

- architecture;
- API documentation;
- database design;
- security design;
- deployment instructions;
- ADRs;
- AI behavior;
- operational procedures; and
- recovery documentation.

Changes to important documentation should therefore be reviewable alongside software changes.

# 26. Pull Request Requirement

Material changes to production software shall normally enter the protected branch through a Pull Request or equivalent reviewed merge process.

A Pull Request establishes the formal review boundary between:

> **Work proposed by an engineer**

and

> **Work accepted into the Deltra codebase.**

# 27. Pull Request Description

A Pull Request should identify:

- purpose of the change;
- related requirement, issue, or work item;
- significant technical changes;
- security implications;
- database implications;
- configuration changes;
- testing performed;
- documentation changes;
- deployment considerations; and
- rollback considerations where applicable.

# 28. Pull Request Template

Deltra should establish a standard Pull Request template.

A typical template may contain:

    Purpose:
    Why is this change required?

    Changes:
    What was modified?

    Security Impact:
    Does this affect authentication, authorization, tenant isolation,
    secrets, customer data, AI permissions, or other security controls?

    Database Impact:
    Are schema or data migrations required?

    Testing:
    What tests were performed?

    AI Assistance:
    Was AI materially used to create or modify this change?

    Deployment:
    Are special deployment steps required?

    Rollback:
    How can the change be reversed or recovered?

    Documentation:
    What documentation was updated?

# 29. AI Assistance Disclosure

Deltra encourages AI-assisted software engineering.

Engineers shall not be required to identify every individual line produced with AI assistance.

However, when AI materially contributes to a Pull Request, the engineer should identify that AI assistance was used.

Example:

    AI Assistance:
    AI was used to generate the initial PostgreSQL migration and unit-test
    structure. Both were manually reviewed and modified before submission.

The purpose is not to stigmatize AI use.

The purpose is engineering transparency.

# 30. Human Ownership of AI-Assisted Changes

The engineer submitting a Pull Request is accountable for the entire change regardless of whether AI generated some or all of its initial content.

The engineer shall not submit AI-generated changes they do not sufficiently understand to accept responsibility for.

Where a Builder Agent submits the Pull Request, no human author exists. Accountability for the change rests with the Approver, who accepts it at the Acceptance Gate (Section 134A) on the basis of the evidence package (Section 134B) and acceptance testing.

A Builder Agent shall describe every change it submits in plain language in the Pull Request, including any part it is uncertain about.

# 31. AI Conversation Is Not Design Documentation

An AI conversation shall not be considered a substitute for repository documentation.

If an important:

- architectural decision;
- security assumption;
- database design;
- business rule;
- deployment requirement; or
- implementation rationale

emerges from an AI interaction, the important conclusion shall be documented in an appropriate Deltra artifact such as:

- code;
- Pull Request;
- ADR;
- README;
- technical document; or
- work item.

# 32. Self-Review Before Pull Request

Before requesting review, the author shall review their own change.

The author should examine:

- the complete diff;
- unintended files;
- debug statements;
- secrets;
- commented-out code;
- incomplete work;
- security implications;
- test results; and
- documentation.

An engineer should never assume:

> “The reviewer will catch it.”

# 33. Build Before Review

Where applicable, code should successfully:

- compile;
- build;
- lint; and
- execute relevant automated tests

before formal human review.

Known failures shall be disclosed rather than hidden.

# 34. Automated Pull Request Checks

Deltra should progressively automate Pull Request validation.

Applicable checks may include:

    Pull Request
         ↓
    Build
         ↓
    Formatting / Lint
         ↓
    Unit Tests
         ↓
    Integration Tests
         ↓
    Static Analysis
         ↓
    Dependency Scan
         ↓
    Secret Scan
         ↓
    Container Scan
         ↓
    Security Tests
         ↓
    Human Review

Not every project requires every check.

Requirements shall correspond to system risk.

# 35. Human Code Review

Material production changes should receive review by another qualified human whenever staffing reasonably permits.

Under Single-Approver Operation (Section 39), per-change review is performed by independent Reviewer Agents, and human review occurs at the milestone gates. The controls in Section 39 compensate for the absence of a second qualified human.

The reviewer shall evaluate more than whether the application appears to work.

Review should consider:

- correctness;
- readability;
- architecture;
- security;
- maintainability;
- testing;
- error handling;
- performance;
- database impact;
- documentation; and
- operational impact.

# 36. Code Review Is Not a Formality

Approval shall not be given merely because:

- the author is senior;
- AI generated the change;
- the change is urgent;
- automated tests pass;
- the author is the CEO;
- the change appears small; or
- the reviewer wants to avoid disagreement.

Approval means the reviewer has performed a reasonable review appropriate to the risk.

# 37. Reviewer Independence

Where staffing permits, the author shall not be the sole reviewer and approver of their own production change.

As Deltra Software Engineering grows, greater independence shall be required for higher-risk systems.

A Builder Agent shall never review its own change. Each Reviewer Agent shall meet the independence requirements of Section 26A.2 of the Deltra AI Development and Acceptable Use Policy.

# 38. CEO-Authored Code

Code authored by the Chief Executive Officer / Executive Product Architect shall be subject to the same engineering review expectations as code authored by other engineers.

Executive position shall not substitute for technical review.

# 39. Single-Approver Operation

This section replaces the Founding-Team Exception of Version 1.0.

Deltra Software Engineering currently operates with one accountable human, the **Approver**, while AI agents perform the production work of software development. In this arrangement, complete separation of duties between people is not possible.

This is a recognized segregation-of-duties limitation. Deltra addresses it through the following compensating controls, all of which shall be in place:

1. **Independent agent review.** Every Pull Request receives review and sign-off from each Reviewer Agent the change requires (Web-App Development Stack Section 18), each independent of the Builder Agent and running on a different Claude model.
2. **Automated gates.** Required automated checks (Section 34) run on every Pull Request. Agents cannot bypass them or change their definitions.
3. **Branch protection.** `main` is protected (Section 111). Only Pull Requests that satisfy Section 48 may merge.
4. **No agent production access.** Agents hold no production credentials and perform no production deployment, production database change, or real-data migration.
5. **Synthetic data.** Development environments contain synthetic data only.
6. **Evidence-based acceptance.** Each milestone produces an evidence package (Section 134B) that the Approver reviews.
7. **Personal acceptance testing.** The Approver performs acceptance testing before each release.
8. **Approver-only release and deployment.** Only the Approver approves releases, creates release tags, and deploys to production.
9. **Recorded decisions.** Every gate decision and every change to agent permissions or CI gates is recorded and retained under Section 127.

While the Engineering Technical Lead role is vacant, its responsibilities under this Standard are held by the Approver.

**Emergency changes.** In an emergency, a Builder Agent may prepare a fix in Development Mode. The Approver reviews the fix, deploys it, and records it under Sections 91–94.

**Disclosure.** When Deltra describes its development controls to a customer, auditor, or examiner, it shall describe this arrangement accurately, including that per-change review is performed by an AI agent and that human approval occurs at the milestone gates.

**Review.** This section shall be reviewed at least annually, and whenever a second qualified human joins Deltra Software Engineering. At that point, human review of high-risk changes should be reintroduced.

# 40. Review Priority

Review effort shall correspond to risk.

Particular attention shall be given to changes involving:

- authentication;
- authorization;
- tenant isolation;
- database security;
- RLS policies;
- credentials;
- encryption;
- customer information;
- destructive operations;
- billing;
- security monitoring;
- infrastructure;
- AI agents;
- AI tool permissions;
- production migrations; and
- privileged administrative functionality.

# 41. Review Comments

Review comments should be constructive and technically specific.

Review discussions shall focus on:

- the code;
- architecture;
- risk;
- maintainability; and
- business requirements.

Review shall not become a competition between engineers.

# 42. Review Finding Severity and Verdict

Human and Reviewer Agent reviews use one vocabulary.

Each finding is rated:

**High**  
Breaks a binding requirement or a security, data-safety, or scope rule. Must be resolved before merge.

**Medium**  
A material gap, such as a missing citation, test, audit event, or logged decision. Must be resolved before merge, or the disagreement recorded in the decision log with its reason.

**Low**  
Clarity, wording, naming, or a hardening suggestion. Resolved at the author's discretion.

Each review ends with one verdict:

**PASS** — no findings.  
**PASS WITH FINDINGS** — no High findings.  
**FAIL** — at least one High finding.

Security issues are rated by their consequence like any other finding; most are High. Questions are written as findings with the severity of the risk they raise. This prevents minor formatting opinions from being confused with material engineering defects.

# 43. Unresolved Findings

A Pull Request shall not be merged while any High finding is unresolved, or while any Medium finding is neither resolved nor recorded in the decision log with the reason it is not being fixed, unless an authorized risk acceptance documents why the change may proceed.

# 44. Approval Requirements

The minimum number of reviewers may vary according to risk.

An initial model should be:

| Change Risk | Minimum Human Review (team with multiple engineers) | Single-Approver Operation (Section 39) |
|-------------|------------------------------------------------------|----------------------------------------|
| Low         | 1 qualified reviewer where practical | Automated checks plus sign-off from each required Reviewer Agent; agent may merge |
| Moderate    | 1 qualified reviewer | Automated checks plus sign-off from each required Reviewer Agent; agent may merge; change summarized in the evidence package |
| High        | 1 qualified senior reviewer | Automated checks plus sign-off from each required Reviewer Agent, including the security reviewer (Section 45); agent may merge; change listed individually in the evidence package for Approver review before release |
| Critical    | Senior engineering review plus additional security or executive approval as appropriate | Explicit Approver approval before merge |

High-risk changes are those described in Sections 40 and 58. Critical changes include any change to agent permissions, CI gate definitions, branch protection, Reviewer Agent definitions, or the pinned version of the governing documents (`docs/standards/`).

**Reviewer Agent sign-off** means a verdict of PASS or PASS WITH FINDINGS on the change as it will be merged, with every finding handled as Section 43 requires. Each report is saved in the milestone evidence package (Web-App Development Stack Section 18).

Automation supplements but does not automatically replace required human review. Under Single-Approver Operation, the Approver's review occurs at the milestone gates rather than on each Pull Request.

# 45. Review of Security-Sensitive Changes

Changes affecting security boundaries shall receive explicit security consideration.

The reviewer should ask:

- Can authentication be bypassed?
- Is authorization enforced server-side?
- Can a user cross tenant boundaries?
- Are privileges excessive?
- Are secrets exposed?
- Is external input trusted?
- Could the operation be replayed?
- Does AI gain new authority?
- Are negative tests present?

# 46. Approval Does Not Transfer Ownership

Code review approval does not transfer primary ownership from the author.

The author remains responsible for addressing defects discovered before and after deployment.

Review establishes shared quality control, not elimination of author accountability.

# 47. Merge Method

Deltra should generally use a merge approach that preserves understandable repository history.

For short-lived feature branches, **squash merge** may be preferred where it produces a cleaner logical history.

A project may use regular merge commits or rebasing when technically justified.

Consistency within a project is more important than personal preference.

# 48. Merge Preconditions

Before merge, applicable conditions shall be satisfied:

- required reviews completed, including sign-off from each required Reviewer Agent under Single-Approver Operation (Section 44);
- required automated tests passed;
- security checks completed;
- unresolved material comments resolved;
- branch reasonably current;
- migrations reviewed;
- documentation updated; and
- deployment requirements understood.

# 49. Bypassing Branch Protection

Branch-protection controls shall not be bypassed merely to accelerate routine work.

Any exceptional bypass should be:

- authorized;
- attributable;
- justified;
- limited; and
- reviewed afterward when material.

# 50. Change Management Principle

Source-code approval and production deployment are related but distinct events.

A Pull Request being merged does not automatically mean:

> **Deploy immediately to production.**

Deployment shall occur according to the applicable release and change-management process.

# 51. Change Categories

Deltra shall classify material production changes into one of the following categories:

1.  Standard Change
2.  Normal Change
3.  Emergency Change

# 52. Standard Change

A **Standard Change** is a low-risk, repeatable, well-understood change performed through an established procedure.

Examples may include:

- routine dependency updates after automated testing;
- standard configuration deployment;
- routine certificate renewal;
- pre-approved application deployment procedures.

A change does not become a Standard Change merely because it is performed frequently.

The procedure and risk must be understood.

# 53. Normal Change

A **Normal Change** includes planned software changes requiring ordinary engineering review and deployment control.

Examples include:

- new features;
- bug fixes;
- database schema changes;
- API changes;
- authorization changes;
- infrastructure changes;
- major dependency updates;
- new integrations;
- AI model changes.

Most software development shall fall into this category.

# 54. Emergency Change

An **Emergency Change** is a change requiring accelerated implementation because delaying the change presents greater risk than following the normal schedule.

Examples may include:

- actively exploited security vulnerability;
- severe production outage;
- data corruption;
- broken authentication;
- cross-tenant exposure;
- critical integration failure.

Emergency status shall not be used merely because planning was poor or a deadline was missed.

# 55. Work-Item Traceability

Material changes should be associated with an identifiable:

- requirement;
- ticket;
- issue;
- change record;
- incident;
- security finding; or
- other work item.

The objective is to preserve the relationship:

    Business Need
         ↓
    Work Item
         ↓
    Pull Request
         ↓
    Commit
         ↓
    Build
         ↓
    Release
         ↓
    Production Deployment

# 56. Work-Item Content

A work item should provide sufficient information to understand:

- problem or requirement;
- affected system;
- requested outcome;
- business priority;
- acceptance criteria where applicable; and
- relevant security or operational considerations.

# 57. Change Risk Assessment

Before production deployment, material changes shall be evaluated for risk.

Considerations include:

- customer impact;
- number of affected users;
- production availability;
- data integrity;
- database changes;
- security impact;
- tenant isolation;
- reversibility;
- external dependencies;
- configuration changes;
- AI behavior changes; and
- operational complexity.

# 58. High-Risk Change Examples

Changes should normally be treated as high risk when they affect:

- authentication;
- authorization;
- tenant boundaries;
- production database schemas containing critical information;
- destructive data operations;
- privileged infrastructure;
- firewall configuration;
- identity providers;
- encryption;
- production AI-agent authority; or
- multiple customer environments.

# 59. Change Approval

Change approval shall correspond to risk.

Routine low-risk deployments may be authorized through established engineering procedures.

Higher-risk changes may require approval from:

- Engineering Technical Lead;
- Security Authority;
- Product Owner;
- Executive Product Architect; or
- another designated authority.

Executive approval shall not be required for every routine deployment.

Under Single-Approver Operation, all production change approval rests with the Approver.

The objective is appropriate governance, not unnecessary bottlenecks.

# 60. Separation of Merge and Deployment Authority

Where practical, Deltra should distinguish:

> **Authority to merge source code**

from

> **Authority to deploy to production.**

As the organization grows, these permissions should become increasingly separated according to system risk.

Under Single-Approver Operation:

- **Merge authority** in Development Mode is held by agents, subject to Sections 44 and 48.
- **Release and deployment authority** is held exclusively by the Approver.

# 61. Continuous Integration

Deltra should progressively implement Continuous Integration.

CI should automatically validate code when practical.

Typical workflow:

    Developer Push
          ↓
    Pull Request
          ↓
    Build
          ↓
    Automated Tests
          ↓
    Security Checks
          ↓
    Review
          ↓
    Merge

# 62. Continuous Delivery

Deltra should design software so approved releases can be deployed through repeatable automation.

Continuous Delivery does not require unattended production deployment.

Production promotion may still require human authorization.

# 63. Production Deployment Automation

Repeated manual deployment steps should progressively be automated.

Automation reduces:

- typing errors;
- forgotten steps;
- environmental inconsistency;
- undocumented changes; and
- dependence upon individual memory.

# 64. Production Builds

Production artifacts should be built through a controlled and repeatable process.

Where practical, production deployment shall use an artifact created by CI rather than rebuilding arbitrary source directly on the production server.

# 65. Source-to-Production Traceability

Deltra should be able to determine the relationship:

    Production Instance
           ↓
    Application Version
           ↓
    Build Artifact
           ↓
    Git Commit
           ↓
    Pull Request
           ↓
    Work Item

This traceability is a major objective of this Standard.

# 66. Application Version Identification

Production applications shall expose or otherwise record an identifiable version.

Version information may include:

- application version;
- Git commit hash;
- build number;
- build date; and
- deployment date.

Operational personnel should be able to determine what software version is currently running.

# 67. Version Numbering

Deltra should generally use Semantic Versioning for software releases where traditional release versioning is useful.

Format:

    MAJOR.MINOR.PATCH

Example:

    2.4.1

General meaning:

**MAJOR**  
Material incompatible architectural or interface change.

**MINOR**  
Backward-compatible feature addition.

**PATCH**  
Backward-compatible bug or security correction.

Not every internal build requires a manually assigned new semantic version.

Automated build identifiers and Git commit hashes may supplement release versions.

# 68. Release Tags

Formal releases should be represented by immutable Git tags where practical.

Example:

    v1.0.0
    v1.1.0
    v1.1.1

Tags should correspond to known source revisions.

Release tags shall not be moved casually to different commits after publication.

# 69. Release Notes

Material releases should contain release notes appropriate to their audience.

Release notes may identify:

- new functionality;
- defects corrected;
- security changes;
- database migrations;
- breaking changes;
- configuration changes;
- known issues; and
- operational considerations.

Internal release notes may contain more technical detail than customer-facing release notes.

# 70. Database Changes

Database changes shall be treated as first-class software changes.

Schema modifications shall not be treated as informal DBA activity separate from application development.

# 71. Database Migration Scripts

Production schema changes shall normally be implemented through version-controlled migration scripts.

Examples:

    migrations/
        001-create-tenants.sql
        002-create-users.sql
        003-add-ticket-priority.sql

The exact naming convention may vary by migration tool.

The important requirement is deterministic ordering and traceability.

# 72. Direct Production Schema Modification

Engineers shall not routinely make undocumented production schema changes through interactive SQL.

Emergency modifications may be necessary but shall subsequently be:

- captured in source control;
- documented;
- reconciled with migration history; and
- reviewed.

# 73. Migration Review

Database migrations shall receive review appropriate to risk.

Review shall consider:

- syntax;
- data integrity;
- locking;
- execution duration;
- existing data;
- indexes;
- constraints;
- rollback;
- application compatibility;
- tenant isolation; and
- security permissions.

# 74. Destructive Database Migrations

Changes involving:

- `DROP`;
- mass `DELETE`;
- destructive transformation;
- column removal;
- table removal;
- irreversible type conversion; or
- major data restructuring

require heightened review.

A current and recoverable backup or other verified recovery method should exist before material destructive changes.

# 75. Expand-and-Contract Migrations

For systems requiring minimal downtime, Deltra should prefer backward-compatible migration patterns where appropriate.

Example:

    Add New Structure
           ↓
    Deploy Compatible Application
           ↓
    Migrate Data
           ↓
    Verify
           ↓
    Remove Old Structure Later

rather than coupling destructive schema replacement to a single deployment when avoidable.

# 76. Database Migration Identity

Database migrations should execute under an identity distinct from the normal application runtime account when practical.

The runtime application should not possess schema-owner privileges solely because deployments require them.

# 77. Configuration Changes

Production configuration shall be treated as controlled change.

Configuration affecting:

- security;
- database connectivity;
- AI models;
- API integrations;
- logging;
- network access;
- authentication;
- authorization;
- feature flags; or
- operational behavior

shall be documented and traceable where practical.

# 78. Secrets Are Not Configuration History

Configuration templates may be source controlled.

Active secrets shall not be committed merely to make configuration reproducible.

Source control should contain:

    DATABASE_PASSWORD=<SECRET>

rather than the actual production password.

# 79. AI Prompt Changes

Production AI prompts shall be treated as software behavior.

Material changes to:

- system prompts;
- production prompt templates;
- AI instructions;
- RAG instructions;
- tool-selection rules

shall be:

- version controlled where practical;
- reviewed;
- tested; and
- associated with an identifiable release or configuration change.

# 80. AI Model Changes

Changing the production AI model may materially change application behavior even when source code remains unchanged.

Changes involving:

- provider;
- model family;
- major model version;
- reasoning configuration;
- model parameters; or
- fallback behavior

shall be treated as controlled application changes.

# 81. AI Agent Permission Changes

Changes granting an AI agent a new tool or privilege shall receive security review appropriate to the privilege.

For example:

    Before:
    AI may create draft ticket.

    After:
    AI may disable Entra user accounts.

is not merely a configuration change.

It represents a substantial change in authority and shall receive heightened review.

# 82. Deployment Preconditions

Before material production deployment, the responsible engineer, or under Single-Approver Operation the Approver, shall verify applicable items including:

- recorded Acceptance Gate and Release Gate approvals (Section 134A);
- no decisions with status Assumed, and no Open decisions, within the released scope;
- approved Pull Request;
- successful build;
- required automated tests;
- required security tests;
- database migration readiness;
- configuration readiness;
- secrets availability;
- deployment procedure;
- rollback or recovery plan;
- monitoring readiness;
- release identifier; and
- required approvals.

# 83. Deployment Record

Material production deployments should create an identifiable deployment record.

The record should identify:

- application;
- environment;
- version;
- commit;
- deployment date and time;
- person or process performing deployment;
- related work item;
- result; and
- rollback activity if applicable.

# 84. Deployment Validation

Deployment is not complete when files are copied or containers start.

Post-deployment validation shall confirm appropriate functions such as:

- application process running;
- health check successful;
- database connectivity;
- API availability;
- authentication;
- critical workflow;
- monitoring;
- error rate; and
- applicable integrations.

# 85. Smoke Testing

Material deployments should include a limited set of high-value post-deployment tests.

Examples:

    Can user authenticate?

    Can customer retrieve authorized ticket?

    Can unauthorized tenant access be denied?

    Can application reach database?

    Can background worker process queued item?

    Can monitoring detect service health?

# 86. Monitoring After Change

Higher-risk changes should receive heightened monitoring after deployment.

Engineers should review:

- errors;
- logs;
- application health;
- latency;
- database behavior;
- security alerts;
- resource use;
- AI behavior; and
- customer-impact indicators.

# 87. Rollback Principle

Material changes shall consider in advance:

> **What will we do if this change fails?**

Rollback or recovery planning shall occur before deployment rather than after failure.

# 88. Rollback Versus Recovery

Not all changes can be safely reversed.

For example, a database migration may make application rollback complex.

Therefore Deltra shall distinguish:

**Rollback**  
Return to a previous application or configuration state.

**Recovery**  
Restore service through another controlled process when direct reversal is impractical.

# 89. Rollback Testing

Rollback or recovery procedures for critical systems should be tested periodically where practical.

A rollback procedure that has never been tested shall not automatically be assumed reliable.

# 90. Failed Deployment

If a deployment fails, the responsible engineer shall determine whether to:

- retry;
- roll back;
- restore;
- correct forward;
- disable affected functionality; or
- activate another recovery procedure.

Repeated uncontrolled experimentation directly against production shall be avoided.

# 91. Emergency Change Procedure

Emergency changes may use an abbreviated workflow when necessary to protect:

- availability;
- customer information;
- security;
- data integrity; or
- critical business operations.

However, emergency status does not eliminate accountability.

# 92. Emergency Change Minimum Requirements

Where circumstances permit, an emergency change shall still identify:

- incident or emergency condition;
- affected system;
- proposed change;
- responsible engineer;
- known risk;
- backup or recovery considerations;
- deployment result; and
- post-change validation.

# 93. Emergency Review

When normal pre-deployment peer review cannot reasonably occur, the emergency change shall receive retrospective review as soon as practical.

The review shall determine:

- whether the change was correct;
- whether source control matches production;
- whether additional testing is required;
- whether security was affected;
- whether documentation requires updates; and
- whether the emergency procedure revealed a process weakness.

# 94. Emergency Changes Must Enter Source Control

An emergency production modification shall not permanently exist only on the production system.

The authoritative repository shall be reconciled so that source control accurately represents production.

# 95. Hotfix Branch

A critical production correction may use:

    hotfix/<description>

Example:

    hotfix/cross-tenant-ticket-access

The hotfix shall follow normal review as much as the circumstances reasonably permit.

# 96. Security Emergency Changes

Emergency security changes shall receive priority when delay creates material risk.

Examples include:

- exposed credential;
- actively exploited vulnerability;
- authorization bypass;
- cross-tenant exposure;
- authentication failure;
- public administrative interface.

Correctness and recoverability remain important even under urgency.

# 97. Change Freeze

Deltra may establish temporary change freezes during:

- critical customer periods;
- major migrations;
- regulatory examinations;
- high-risk operational periods;
- significant incidents; or
- holidays and staffing constraints

when production risk justifies limiting nonessential change.

Security or emergency changes may still proceed through appropriate approval.

# 98. Failed Change Review

Material failed deployments should be reviewed.

The objective is to identify whether failure resulted from:

- incorrect requirement;
- code defect;
- inadequate test;
- configuration error;
- migration failure;
- dependency;
- incomplete documentation;
- insufficient review;
- environment difference;
- process failure; or
- unexpected external behavior.

The purpose is organizational learning, not blame.

# 99. Post-Implementation Review

High-risk or unusually significant changes may receive a Post-Implementation Review.

Questions should include:

- Did the change achieve its objective?
- Were unexpected problems encountered?
- Did the risk assessment prove accurate?
- Did rollback or recovery work?
- Did monitoring provide sufficient visibility?
- Were customer impacts identified?
- What should be changed next time?

# 100. Reverting Changes

A revert shall itself be treated as a production change.

The reason for the revert should be documented.

A reverted change may require:

- database considerations;
- configuration changes;
- security analysis; and
- customer communication.

# 101. Change Documentation in Git

Where practical, Git history should allow a future engineer to understand important system evolution without requiring access to one employee’s memory.

Pull Requests, commits, ADRs, and release tags collectively provide that history.

# 102. Architecture Decision Records

A change involving a significant architectural decision shall reference or create an ADR where appropriate.

Examples:

    ADR-0015-use-nats-jetstream-for-event-delivery.md

    ADR-0016-centralize-ai-provider-access.md

    ADR-0017-separate-application-runtime-and-database-owner.md

The Pull Request should link to the ADR.

# 103. Documentation Changes

When code changes alter:

- architecture;
- deployment;
- APIs;
- database schemas;
- configuration;
- security;
- AI behavior;
- operations; or
- troubleshooting

the related documentation shall be updated as part of the same change or explicitly tracked as required follow-up work.

# 104. Documentation Drift

Documentation known to be inaccurate shall be considered an engineering defect.

Documentation should not remain knowingly wrong merely because updating it does not affect application execution.

# 105. Code Ownership

Every significant application should have identifiable technical ownership.

Ownership does not mean only one person is permitted to modify the system.

Ownership means someone is accountable for:

- technical health;
- review;
- maintenance;
- security;
- documentation;
- release practices; and
- lifecycle decisions.

# 106. CODEOWNERS or Equivalent

As the engineering organization grows, Deltra should use repository mechanisms such as `CODEOWNERS` or equivalent functionality where useful.

Sensitive areas may require designated reviewers.

Examples include:

    /database/security/
    /authentication/
    /authorization/
    /deploy/
    /ai-tools/

# 107. Removal of Contributor Access

When an employee, contractor, or vendor no longer requires repository access, access shall be removed promptly.

Offboarding shall consider:

- Git provider account;
- SSH keys;
- access tokens;
- signing keys;
- CI/CD credentials;
- package registries;
- container registries; and
- deployment systems.

# 108. Personal Access Tokens

Source-control Personal Access Tokens shall be:

- protected as secrets;
- appropriately scoped;
- time-limited where practical;
- revoked when unnecessary; and
- avoided when more appropriate service identities exist.

# 109. SSH Keys

SSH keys used for source-control access shall be:

- individually attributable where practical;
- protected;
- revoked upon offboarding;
- replaced if compromised; and
- not casually shared.

# 110. Service Accounts and CI Identities

CI/CD systems shall use dedicated machine identities or tokens rather than personal developer credentials where practical.

Automated deployment should not permanently depend upon an individual’s personal account.

AI agent identities shall be separate from the Approver's personal accounts and credentials. They shall not hold production deployment rights.

# 111. Branch Protection

Protected production branches should enforce applicable controls such as:

- Pull Request required;
- approvals required;
- automated checks required;
- force push disabled;
- deletion disabled;
- unresolved conversations blocked; and
- administrator bypass restricted.

Controls should correspond to repository risk.

# 112. Release Protection

Production deployment environments should use controls preventing arbitrary unreviewed source from being deployed.

Only authorized:

- branches;
- tags;
- artifacts; or
- workflows

should be eligible for production deployment.

# 113. Production Artifact Registry

Where Deltra uses container images or packaged artifacts, production artifacts should be stored in a controlled repository or registry.

Artifacts shall be identifiable by version.

Using mutable tags such as:

    latest

as the sole method of identifying production software should be avoided.

# 114. Immutable Release Identification

A deployment should reference an immutable identifier such as:

- Git commit SHA;
- release tag;
- artifact digest; or
- package version.

This enables Deltra to identify exactly what is running.

# 115. Dependency Changes

Dependency changes shall appear in source control.

Major dependency upgrades should explain:

- reason;
- compatibility;
- security impact;
- breaking changes;
- testing; and
- rollback considerations.

# 116. Automated Dependency Updates

Automated dependency-update tools may create Pull Requests.

Automated creation shall not imply automatic approval.

The same risk-based review principles apply.

# 117. Large Refactors

Large refactors should be divided into manageable changes where practical.

A Pull Request changing thousands of unrelated lines is difficult to review reliably.

Refactoring should avoid combining major behavior changes with widespread mechanical rewrites when separation is practical.

# 118. Formatting-Only Changes

Large automated formatting changes should be separated from functional changes where practical.

This allows reviewers to identify material behavior changes more easily.

# 119. Generated Code

Generated source code shall identify its generation process where appropriate.

Engineers shall understand whether generated code should be:

- committed;
- rebuilt during CI;
- regenerated from a schema; or
- treated as an artifact.

Manual modification of generated files should be avoided when those changes will later be overwritten.

# 120. Experimental Code

Experimental or proof-of-concept code shall not be merged into production branches merely because it demonstrates successful behavior.

Before becoming production software, experimental code shall satisfy applicable:

- review;
- security;
- testing;
- documentation;
- deployment; and
- operational requirements.

# 121. Feature Flags

Feature flags may be used to separate deployment from feature activation.

Feature flags shall not be used as substitutes for authorization.

Security-sensitive functionality shall remain protected regardless of feature-flag state.

Unused feature flags should eventually be removed.

# 122. Backward Compatibility

Changes to APIs, data formats, agents, and distributed systems shall consider backward compatibility.

Breaking changes should be intentional, documented, and coordinated.

# 123. Client and Agent Compatibility

Where Deltra operates distributed software such as endpoint agents, releases shall consider that not all clients may upgrade simultaneously.

Server APIs should account for supported client versions where appropriate.

# 124. Change Windows

High-risk production changes may be scheduled during controlled maintenance windows when:

- technical personnel are available;
- customers can be notified;
- rollback can be performed;
- third-party support is available; or
- business impact can be minimized.

Routine low-risk deployments need not automatically require after-hours maintenance windows.

# 125. Customer Notification

Changes materially affecting customer availability, interfaces, workflows, or obligations shall include appropriate customer communication when necessary.

Engineering shall coordinate with the responsible business owner before deployment.

# 126. Regulatory and Audit Considerations

For software supporting regulated financial institutions, change records should be sufficiently clear that Deltra can demonstrate reasonable control over:

- source changes;
- approvals;
- security review;
- testing;
- production access;
- database changes; and
- releases.

The objective shall be genuine engineering control rather than creation of documentation solely for an auditor.

# 127. Evidence Retention

Records associated with significant changes should be retained according to applicable Deltra retention requirements.

Evidence may include:

- Pull Requests;
- approvals;
- test results;
- CI logs;
- release records;
- deployment history;
- security scan results;
- change tickets;
- ADRs; and
- emergency-change records.

# 128. No Approval by AI

AI may assist with:

- code review;
- vulnerability identification;
- test generation;
- documentation;
- change-risk analysis; and
- Pull Request summaries.

AI shall not be considered the accountable approval authority for production changes.

An AI-generated:

    Looks good to me.

does not constitute human approval.

Under Single-Approver Operation, Reviewer Agent sign-off (Section 44) is a merge precondition in Development Mode. It is not approval of a production change.

# 129. AI Code Review

AI code review is encouraged as an additional review layer.

AI may be particularly useful for:

- identifying overlooked edge cases;
- suggesting tests;
- detecting suspicious logic;
- reviewing documentation;
- identifying possible vulnerabilities;
- explaining complex changes.

AI review shall supplement, not automatically replace, human review required by this Standard.

# 130. Human Reviewer Use of AI

A human reviewer may use AI to assist the review process.

However, the human reviewer remains accountable for the approval.

The reviewer should not approve a Pull Request they have not reasonably examined merely because an AI reviewer reported no issues.

# 131. Change Management for AI-Generated Applications

An application substantially generated through AI shall receive the same change controls as conventionally written software.

Development speed does not reduce requirements for:

- source control;
- review;
- testing;
- release;
- security;
- documentation; or
- recovery.

# 132. Measuring Engineering Change

Deltra shall not evaluate engineers primarily according to:

- number of commits;
- Pull Requests created;
- lines changed;
- code generated; or
- frequency of AI use.

Change metrics shall be interpreted in the context of:

- quality;
- reliability;
- business value;
- security;
- maintainability; and
- operational outcomes.

# 133. Small-Team Operation

During the early development of Deltra Software Engineering, process shall remain proportional.

A two-person engineering organization does not require the bureaucracy of a 500-person software company.

However, the foundational controls shall still exist:

    Requirement
        ↓
    Branch
        ↓
    Commit
        ↓
    Pull Request
        ↓
    Review
        ↓
    Automated Testing
        ↓
    Merge
        ↓
    Release
        ↓
    Deployment
        ↓
    Validation

The process can become more sophisticated as Deltra grows without fundamentally changing its structure.

When AI agents perform the development work and one human holds approval authority, Section 39 applies.

# 134. Expected Normal Development Workflow

The preferred Deltra workflow for a team of human engineers shall be:

    1. Identify Business Requirement
                 ↓
    2. Create Work Item
                 ↓
    3. Create Short-Lived Branch
                 ↓
    4. Human + AI Development
                 ↓
    5. Engineer Self-Review
                 ↓
    6. Build and Automated Testing
                 ↓
    7. Create Pull Request
                 ↓
    8. Human Code Review
                 ↓
    9. Security Review When Required
                 ↓
    10. Merge into Protected main
                 ↓
    11. CI Creates Release Artifact
                 ↓
    12. Production Deployment Approval
                 ↓
    13. Deploy
                 ↓
    14. Smoke Test / Health Validation
                 ↓
    15. Monitor
                 ↓
    16. Close Change

Under Single-Approver Operation, the workflow shall be:

    DEVELOPMENT MODE: agents, synthetic data only

    1. Approver establishes business requirement (SRS)
                 ↓
    2. Work item created, citing requirement IDs
                 ↓
    3. Builder Agent creates short-lived branch
                 ↓
    4. Builder Agent writes tests from acceptance criteria
                 ↓
    5. Builder Agent implements; Open decisions recorded
       as Assumed in the decision log
                 ↓
    6. Builder Agent self-review, build, automated tests
                 ↓
    7. Pull Request with requirement IDs, decisions made,
       and AI disclosure
                 ↓
    8. Automated checks
                 ↓
    9. Review and sign-off by each required Reviewer Agent
                 ↓
    10. Merge into protected main (agent)

    MILESTONE: Approver

    11. Agents assemble the evidence package
                 ↓
    12. Acceptance Gate: Approver reviews evidence, ratifies,
        changes, or rejects decisions, and performs
        acceptance testing
                 ↓
    13. Release Gate: Approver approves release;
        CI builds the release artifact; Approver tags it

    PRODUCTION MODE: Approver only

    14. Approver deploys using the runbook
                 ↓
    15. Smoke Test / Health Validation
                 ↓
    16. Monitor
                 ↓
    17. Close Change

Rework from a gate decision returns to step 2 as a new or reopened work item.

# 134A. Milestone Gates

Under Single-Approver Operation, work passes through three gates, each decided by the Approver.

| Gate | When | Approver decides | Recorded result |
|------|------|------------------|-----------------|
| **Requirements Gate** | Before development of a new application or major scope | Requirements are sufficient to begin. Open decisions may remain; agents will record assumptions. | Approved SRS version |
| **Acceptance Gate** | At each milestone | Each Assumed decision is Ratified, Changed, or Rejected. The milestone passes acceptance testing. | Gate record, updated decision log, list of rework items |
| **Release Gate** | Before production deployment | The release may enter production. | Release approval, release tag, deployment record |

A milestone is a coherent, testable increment of functionality defined in the work plan. Milestones should be sized so that the Approver can test them in one sitting.

Agents shall batch questions for the Acceptance Gate rather than interrupt the Approver, except for the stops required by Section 26A.5 of the Deltra AI Development and Acceptable Use Policy.

Promotion to production requires that no decision within the released scope remain Assumed or Open.

# 134B. Evidence Package

At each milestone, agents shall assemble an evidence package that allows the Approver to accept the work without reading every line of code.

The evidence package shall contain:

- **Scope:** the requirement IDs delivered in the milestone;
- **Summary:** a plain-language description of what changed and why;
- **Traceability:** a matrix linking each requirement to its tests and their results;
- **Test results:** automated test results, plus a list of the acceptance tests the Approver should perform personally, with step-by-step instructions;
- **Decision log:** every entry with status Assumed, with the agent's recommendation;
- **Review findings:** each Reviewer Agent report (saved in the milestone's `reviews/` folder), how each finding was resolved, and any that remain open;
- **Security results:** static analysis, dependency, and secret scan results;
- **Database changes:** a summary of schema and migration changes;
- **High-risk changes:** each high-risk change listed individually (Section 44);
- **Limitations:** anything the agents could not do, were uncertain about, or chose to defer; and
- **Identification:** commit hashes and release-candidate version.

For the Release Gate, the evidence package shall also include the deployment runbook, the rollback or recovery plan, and any migration scripts the Approver will run.

The evidence package shall be stored in the repository or release record and retained under Section 127.

# 135. Minimum Pull Request Checklist

Before approval of a material Pull Request, the following should be considered.

## Purpose

- Is the reason for the change clear?
- Is a work item or requirement identified?

## Code

- Is the implementation understandable?
- Does the author understand AI-generated portions?
- Is unnecessary complexity avoided?

## Security

- Does authorization change?
- Does tenant isolation change?
- Are new secrets introduced?
- Is external input properly validated?
- Does an AI agent receive new authority?

## Database

- Is a migration required?
- Is the migration safe?
- Is existing data considered?
- Is rollback or recovery understood?

## Testing

- Does the build succeed?
- Do automated tests pass?
- Are negative tests needed?
- Are security tests needed?

## Operations

- Is configuration changing?
- Is monitoring affected?
- Is deployment documented?
- Is rollback understood?

## Documentation

- Have applicable documents been updated?
- Is an ADR required?

# 136. Minimum Production Change Checklist

Before a material production deployment:

- Approved source revision identified.
- Required Pull Request approvals completed.
- Required CI checks passed.
- Security findings evaluated.
- Database migrations reviewed.
- Configuration prepared.
- Production secrets available through approved mechanisms.
- Deployment method understood.
- Rollback or recovery method understood.
- Required approvals obtained.
- Under Single-Approver Operation: Acceptance Gate and Release Gate recorded, and no Assumed or Open decisions in the released scope.
- Release version identified.
- Monitoring available.
- Post-deployment validation planned.

After deployment:

- Deployment result recorded.
- Application health verified.
- Critical workflow tested.
- Logs reviewed for unexpected errors.
- Security behavior verified where applicable.
- Monitoring confirmed.
- Change record completed.

# 137. Minimum Emergency Change Checklist

For an emergency change:

- Emergency condition identified.
- Responsible engineer identified.
- Proposed corrective action understood.
- Recovery consideration documented.
- Source control used where circumstances permit.
- Available review performed.
- Change deployed.
- Production validated.
- Repository reconciled with production.
- Retrospective review completed.
- Follow-up work created where necessary.

# 138. Source Control and Change Management Summary

Every Deltra engineer should understand the following:

> **Git is the authoritative history of Deltra software.**
>
> **Production code belongs in Deltra-controlled repositories.**
>
> `main` **represents releasable software.**
>
> **Use short-lived branches.**
>
> **Write commits another engineer can understand.**
>
> **Never intentionally commit active secrets.**
>
> **Review your own diff before asking someone else to review it.**
>
> **AI may help write code, but the submitting engineer, or the Approver who accepts agent-produced work, owns the result.**
>
> **Agents build and merge in development; only the Approver releases and deploys.**
>
> **A Pull Request is an engineering review boundary, not administrative paperwork.**
>
> **The CEO’s code receives review too.**
>
> **Security-sensitive changes receive greater scrutiny.**
>
> **Database migrations are source-controlled software changes.**
>
> **Production AI prompts and agent permissions are configuration/code changes and must be governed accordingly.**
>
> **A merge is not automatically a production deployment.**
>
> **Every deployed version should be traceable to source.**
>
> **Every material change should have a recovery strategy.**
>
> **Emergency changes remain accountable changes.**
>
> **Production shall not contain undocumented changes that do not exist in source control.**
>
> **AI may review a change; a human approves the change.**

# 139. Governing Change-Control Principle

The governing source-control and change-management principle of Deltra Software Engineering shall be:

> **No material production change shall depend upon undocumented knowledge, uncontrolled source code, unreviewed authority, or an inability to determine what changed and how to recover from it. Deltra shall preserve a traceable chain from business requirement to source code, review, testing, release, deployment, and production operation.**

## Document History

| Version | Date            | Status | Description                                                                 |
|---------|-----------------|--------|-----------------------------------------------------------------------------|
| 1.0     | August 19, 2026 | Draft  | Initial Deltra Source Control, Code Review, and Change Management Standard. |
| 1.1     | November 3, 2026 | Draft | Replaced Section 39 (Founding-Team Exception) with Single-Approver Operation and its compensating controls. Added a Single-Approver column to Section 44. Added the Single-Approver workflow to Section 134 and new Sections 134A (Milestone Gates) and 134B (Evidence Package). Updated Sections 2, 30, 35, 37, 48, 59, 60, 82, 110, 128, 133, 136, and 138 for AI agents and the Approver role. Assigned the vacant Engineering Technical Lead responsibilities to the Approver. Removed drafting commentary following the document history. Revised before approval on 2026-11-04: one finding vocabulary for human and agent review, High, Medium, and Low with a PASS, PASS WITH FINDINGS, or FAIL verdict (Sections 42 and 43); several Reviewer Agents and a definition of sign-off (Sections 35, 37, 39, 44, 48, 128, 134, 134B); `docs/standards/` changes made Critical (Section 44). |
