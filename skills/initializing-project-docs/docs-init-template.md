# Docs Initialization Template

Use these section skeletons to create first-version docs directly in the current session.

Canonical path rules (mandatory):
- Use `docs/knowledge/` for all knowledge docs.
- Do not create `docs/knowledges/`.
- If both folders exist, consolidate content into `docs/knowledge/` and remove plural-path references.

Hard requirements for final docs:
- Fill every section with concrete, evidence-backed content.
- Do not leave placeholders such as TBD, TODO, "...", or "to be decided".
- Include explicit assumptions where evidence is missing.
- Add source-file evidence in each doc's "Source Files Used" section.
- End the initialization run with a completeness report listing generated files, coverage decisions, unknowns, and follow-ups.
- Validate that final generated file paths use `docs/knowledge/*.md` only.

## docs/design/architecture.md

```markdown
# Architecture

## 1. Purpose and Scope
- What this project is
- In-scope vs out-of-scope

## 2. Goals and Non-Goals
### Goals
- ...
### Non-Goals
- ...

## 3. System Context
- External dependencies
- Runtime environment
- Core constraints

## 4. Component Map
| Component | Responsibility | Key Files | Depends On |
|---|---|---|---|
| ... | ... | ... | ... |

## 5. Runtime Flows
### Primary flow
1. ...
2. ...

### Edge flow
1. ...
2. ...

## 6. Data and State Model
- Core entities/state
- Lifecycle and transitions

## 7. Interfaces and Contracts
- API/CLI/events/config contracts

## 8. Configuration and Environment
- Env vars
- Config files/defaults

## 9. Error Handling and Reliability
- Failure modes
- Recovery behavior

## 10. Security and Privacy Notes
- Sensitive data handling
- Access assumptions

## 11. Performance Notes
- Hot paths
- Known bottlenecks

## 12. Observability and Debugging
- Logs/metrics/tracing/debug entrypoints

## 13. Testing Strategy and Coverage Map
| Area | Existing Tests | Missing Tests | Priority |
|---|---|---|---|
| ... | ... | ... | ... |

## 14. Operations Notes
- Build/run/deploy basics

## 15. Risks and Open Questions
- Risk: ...
- Open question: ...

## 16. Glossary
- Term: meaning

## 17. Source Files Used
- path/to/file
- path/to/file

## 18. Completeness Report
- Generated files
- Coverage decisions
- Unknowns and assumptions
- Follow-up recommendations
```

## docs/knowledge/<topic>.md

```markdown
# Knowledge: <Topic>

## 1. Why This Matters
- ...

## 2. Trigger Conditions
- ...

## 3. Canonical Workflow
1. ...
2. ...

## 4. Commands/APIs/Procedures
- ...

## 5. Source Types
- Official docs / vendor docs
- Web research / verified public references
- User-provided facts / project constraints
- Other evidence captured during initialization

## 5. Edge Cases and Failure Patterns
- ...

## 6. Validation Checklist
- [ ] ...
- [ ] ...

## 7. Related Modules
- docs/module/<module>.md

## 8. Source Materials Used
- path/to/file or URL
- path/to/file or URL

## 9. Completeness Notes
- Scope covered by this knowledge doc
- Known limitations or unknowns
```

## docs/module/<module>.md

```markdown
# Module: <Module Name>

## 1. Responsibility
- ...

## 2. Boundaries and Dependencies
- ...

## 3. Entry Points and Public Surface
- ...

## 4. Internal Structure
| Part | Role | Key Symbols | File |
|---|---|---|---|
| ... | ... | ... | ... |

## 5. Control Flow
### Main flow
1. ...
2. ...

### Error flow
1. ...
2. ...

## 6. Data Contracts
- Inputs/outputs/state assumptions

## 7. Configuration Touchpoints
- Env vars/config keys/defaults

## 8. Failure Modes and Safeguards
- ...

## 9. Testing and Verification
- Existing tests
- Missing tests
- Quick verification commands

## 10. Change Checklist
- [ ] API contract checked
- [ ] Backward compatibility checked
- [ ] Docs consistency checked
- [ ] Tests updated or justified

## 11. Source Files Used
- path/to/file
- path/to/file

## 12. Completeness Notes
- Scope covered by this module doc
- Known limitations or unknowns
```
