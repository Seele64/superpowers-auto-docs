# Docs Initialization Template

Use these section skeletons to create first-version docs directly in the current session.

Canonical path rules (mandatory):
- Use `docs/design/` for design/spec docs (what and why).
- Use `docs/modules/` (plural) for implementation docs (how).
- Use `docs/knowledge/` (singular) for references and external knowledge.
- Do not create non-canonical legacy path variants.

Hard requirements for final docs:
- Fill every section with concrete, evidence-backed content.
- Do not leave placeholders such as TBD, TODO, "...", or "to be decided".
- Include explicit assumptions where evidence is missing.
- Write all documentation narrative in English; non-English text is allowed only for quoted source material when needed.
- Add source-file evidence in each doc's "Source Files Used" section.
- End the initialization run with a completeness report listing generated files, coverage decisions, unknowns, and follow-ups.
- Validate that final generated file paths use `docs/design/*.md`, `docs/modules/*.md`, and `docs/knowledge/*.md`.
- If `docs/superpowers/plans/` or `docs/superpowers/specs/` exists, review and cite relevant files.

Mode rules (mandatory):
- Bootstrap mode (docs missing): create baseline docs set.
- Refresh mode (docs already exist): re-check codebase evidence first, then update stale sections in place.
- In refresh mode, do not rewrite stable docs from scratch unless user explicitly requested full rewrite.

Coverage balance gates (hard requirements):
- Must generate at least 1 file in `docs/design/*.md`.
- Must generate at least 1 file in `docs/modules/*.md`.
- `docs/knowledge/*.md` is optional; create only when external sources are used.
- If `docs/knowledge/*.md` exists, each file must include:
  - at least one external source entry
  - at least one related design doc link
  - at least one related module doc link
- Do not produce a knowledge-heavy baseline with zero design docs.

Optional superpowers context (if present):
- `docs/superpowers/plans/*.md`
- `docs/superpowers/specs/*.md`
- Treat these as supporting evidence for initialization and refresh decisions; do not ignore them when they exist.
- Relevance selection:
  - include files whose filename/headings match current module/topic keywords
  - include files linked by related architecture/design/module docs
  - if no direct match, include at least the most recently updated file from each existing directory

Module granularity rules (hard requirements):
- Keep each `docs/modules/*.md` file contract-focused and scannable.
- Put volatile, feature-by-feature walkthroughs in `docs/design/*.md` and link from module docs.
- Move long test matrices and operational checklists out of module docs unless explicitly required by user.
- If one module doc starts covering multiple independent features in detail, split into linked topic docs.

Refresh-mode checks (hard requirements):
- Map changed code areas to existing docs sections before editing.
- Mark each affected section as up-to-date, stale, missing, or structurally outdated.
- Patch stale/structurally outdated sections first; create new docs only when coverage is missing.
- Produce a component boundary map before refresh edits.
- If a module doc contains deep detail for multiple independent components, split those details into separate linked docs.

Component split output (mandatory):
- Include a table in your completion report:
  - Component
  - Owning code paths
  - Target detail doc
  - Status (updated/new/unchanged)

## docs/architecture.md

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

## docs/design/<topic>.md

```markdown
# Design: <Topic>

## 1. Why This Matters
- ...

## 2. Problem Statement
- ...

## 3. Goals and Non-Goals
- Goals: ...
- Non-Goals: ...

## 4. Options Considered
- Option A: ...
- Option B: ...
- Chosen approach and tradeoffs: ...

## 5. Constraints and Assumptions
- ...

## 6. Canonical Workflow
1. ...
2. ...

## 7. Interfaces and Contracts
- ...

## 8. Edge Cases and Failure Patterns
- ...

## 9. Validation Checklist
- [ ] ...
- [ ] ...

## 10. Related Modules
- docs/modules/<module>.md

## 11. Source Files Used
- path/to/file
- path/to/file

## 12. Completeness Notes
- Scope covered by this design doc
- Known limitations or unknowns
```

## docs/modules/<module>.md

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

## 5. Data Contracts
- Inputs/outputs/state assumptions

## 6. Configuration Touchpoints
- Env vars/config keys/defaults

## 7. Failure Modes and Safeguards
- ...

## 8. Related Design Docs
- docs/design/<topic>.md (feature workflows, tradeoffs, detailed validation)
- docs/design/<topic>.md

## 9. Source Files Used
- path/to/file
- path/to/file

## 10. Completeness Notes
- Scope covered by this module doc
- Known limitations or unknowns
```

Notes:
- Keep module docs concise enough for one-pass scanning.
- Do not duplicate detailed control-flow walkthroughs already documented in design docs.
- If reviewers ask for one-file readability, keep this file as an index and improve navigation links instead of inlining all details.
- If one module covers multiple independent components, keep the module summary unified but split deep component behavior into separate linked detail docs.

## docs/knowledge/<topic>.md

```markdown
# Knowledge Reference: <Topic>

## 1. Context
- Why this reference exists
- Where it applies in this project

## 2. External Sources
| Source | Link | Relevance | Last Verified |
|---|---|---|---|
| ... | ... | ... | ... |

## 3. Key Takeaways
- ...

## 4. Project-Specific Interpretation
- How external guidance maps to our codebase
- What we intentionally do differently

## 5. Risks and Caveats
- Version mismatch risk
- Deprecated guidance risk
- Security/compliance caveats

## 6. Verification Checklist
- [ ] Source links are still reachable
- [ ] Version numbers are still current
- [ ] Guidance still matches current architecture

## 7. Related Docs
- docs/design/<topic>.md
- docs/modules/<module>.md

## 8. Source Files Used
- path/to/file
- path/to/file

## 9. Completeness Notes
- Scope covered by this reference doc
- Known limitations or unknowns
```
