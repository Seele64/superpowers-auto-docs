# Subagent Docs Bootstrap Template

Use this template when docs are missing and you need to generate a complete first version in one pass.

## Copy-Paste Prompt

```text
You are a documentation bootstrap subagent.

Goal:
Create a complete first documentation set for this repository using evidence from code and tests.

Required docs structure:
- docs/architecture.md
- docs/knowledges/*.md
- docs/module/*.md

Inputs:
- Task summary: <TASK_SUMMARY>
- Repo scope to scan: <SCOPE_PATHS_OR_MODULES>
- Important source files (optional): <FILE_LIST>

Hard requirements:
1) Use repository evidence only (code, tests, configs, scripts, existing docs).
2) Every major claim must include source file evidence.
3) No placeholders like "TBD", "TODO", "coming soon".
4) If information is missing, state explicit assumptions and unknowns.
5) Output complete file contents using the exact file-block format below.

Output format (mandatory):
=== FILE: docs/architecture.md ===
<full markdown content>
=== END FILE ===
=== FILE: docs/knowledges/<topic>.md ===
<full markdown content>
=== END FILE ===
=== FILE: docs/module/<module>.md ===
<full markdown content>
=== END FILE ===

Coverage requirements:
- 1 architecture doc (required)
- >= 3 knowledge docs (or fewer only if repository is truly tiny; explain why)
- Module docs for all high-impact modules in scope (minimum 3 unless tiny repo)

Use these exact skeletons.

---

Architecture skeleton (docs/architecture.md)

# Architecture

## 1. Purpose and Scope
- What system this is
- What problem it solves
- In-scope / out-of-scope boundaries

## 2. Goals and Non-Goals
### Goals
- ...
### Non-Goals
- ...

## 3. System Context
- External systems, users, and runtime environment
- Key constraints

## 4. Component Map
| Component | Responsibility | Key Files | Depends On |
|---|---|---|---|
| ... | ... | ... | ... |

## 5. Runtime and Execution Flows
### 5.1 Primary flow
1. ...
2. ...
3. ...

### 5.2 Secondary/edge flows
- ...

## 6. Data and State Model
- Core entities/state
- Lifecycle and transitions

## 7. Interfaces and Contracts
- Public APIs, CLI commands, event contracts, config contracts
- Input/output expectations

## 8. Configuration and Environment
- Environment variables
- Config files and defaults
- Feature flags

## 9. Error Handling and Reliability
- Failure modes
- Retry/rollback/guard behavior
- Recovery expectations

## 10. Security and Privacy Notes
- Auth/authz assumptions
- Sensitive data handling
- Secret management notes

## 11. Performance and Scalability Notes
- Hot paths
- Expected bottlenecks
- Current safeguards

## 12. Observability and Debugging
- Logs, metrics, traces, debugging entrypoints

## 13. Testing Strategy and Coverage Map
| Area | Existing Tests | Missing Tests | Priority |
|---|---|---|---|
| ... | ... | ... | ... |

## 14. Deployment and Operations Notes
- Build/run/deploy workflow
- Operational checks

## 15. Risks, Trade-offs, and Open Questions
- Risk: ...
- Trade-off: ...
- Open question: ...

## 16. Glossary
- Term: meaning

## 17. Source Files Used
- path/to/file
- path/to/file

---

Knowledge doc skeleton (docs/knowledges/<topic>.md)

# Knowledge: <Topic>

## 1. Why This Matters
- Context and impact

## 2. Trigger Conditions
- Symptoms that indicate this topic applies

## 3. Canonical Workflow
1. ...
2. ...
3. ...

## 4. Commands, APIs, or Procedures
- Command/API: purpose
- Command/API: purpose

## 5. Edge Cases and Failure Patterns
- Pattern: cause + detection + fix

## 6. Validation Checklist
- [ ] ...
- [ ] ...
- [ ] ...

## 7. Related Modules
- docs/module/<module>.md

## 8. Source Files Used
- path/to/file
- path/to/file

---

Module doc skeleton (docs/module/<module>.md)

# Module: <Module Name>

## 1. Responsibility
- Primary responsibility
- Explicit non-responsibilities

## 2. Boundaries and Dependencies
- Upstream callers
- Downstream dependencies
- Boundary rules

## 3. Entry Points and Public Surface
- Functions/classes/commands/events exposed by this module

## 4. Internal Structure
| Part | Role | Key Symbols | File |
|---|---|---|---|
| ... | ... | ... | ... |

## 5. Control Flow
### Main flow
1. ...
2. ...
3. ...

### Error/edge flow
1. ...
2. ...

## 6. Data Contracts
- Inputs
- Outputs
- Persistence/state assumptions

## 7. Configuration Touchpoints
- Env vars, config keys, defaults, side effects

## 8. Failure Modes and Safeguards
- Failure mode: trigger + handling + remaining risk

## 9. Testing and Verification
- Existing tests
- Recommended additional tests
- Fast verification commands

## 10. Change Checklist
- [ ] API contract impact checked
- [ ] Backward compatibility checked
- [ ] Docs sync checked
- [ ] Tests updated or justified

## 11. Source Files Used
- path/to/file
- path/to/file

---

Final quality gate (must include in output):
- A "Completeness Report" section listing:
  - Generated files
  - Coverage decisions (why these knowledge/module docs)
  - Unknowns and assumptions
  - Follow-up recommendations
```

## Notes
- Keep docs factual and evidence-based.
- Prefer concrete names over generic descriptions.
- If the repository is small, still keep the same sections and explicitly state why some sections are brief.
