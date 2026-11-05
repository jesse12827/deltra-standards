# deltra-standards

The governing documents of the Deltra software factory: the **fixed plant's** documentation. Each
document has exactly one master, here.

**New to the factory? Read [Start Here](Start_Here.md) first.** It explains the factory in a few pages
and tells you what to read, and when.

| Document | Version | Governs |
|---|---|---|
| [Start Here](Start_Here.md) | 1.1 Approved | Orientation: roles, workflow, the rules never broken, where to find answers, what to read when, the Approver's role, a glossary, and a first-week exercise |
| [Web-App Development Stack](Web-App_Development_Stack.md) | 2.1 Approved | How every web application is built: stack, layout, security, testing, deployment, agent files |
| [Software Architecture Document — Web Application Shell](Software_Architecture_Document_Web_Application_Shell.md) | 2.0 Approved | The page shell every application inherits from the reference template |
| [AI Development and Acceptable Use Policy](deltra-AI-development-and-acceptable-use-policy.md) | 1.1 Approved | AI use at Deltra; Section 26A governs agents as the development workforce |
| [Source Control, Code Review, and Change Management Standard](deltra-source-control-code-review-and-change-management-standard.md) | 1.1 Approved | How changes flow: reviews, findings, gates, evidence, releases |
| [Analogy of a Physical Factory and a Software Factory](analogy_of_physical-factory_and_software-factory.md) | 1.1 Approved | Design reference; v2.0 (AI software factory, with retooling) planned |

## How projects use this repository

Each application repository includes this repository as a Git submodule at `docs/standards/`,
pinned to a specific commit or tag (Stack Section 6). Agents read the documents there; they never
edit them.

- **Change a document here, never in a project.** Changes follow the Change Management Standard and
  need the Approver's approval.
- **Tag each approved set** (for example `v2026.11.1`), so applications can pin a known version.
- **An application moves to a new version deliberately:** `make standards-pin STANDARDS_REF=<tag>` in
  the application, then a run of the `doc-consistency-reviewer`. Moving the pin is a Critical change.

## Formats

Markdown is the master format. Word copies are generated from the Markdown when needed and are never
edited directly.

Application documents (each application's SRS, decision log, and evidence) live in the application's
own repository, not here.
