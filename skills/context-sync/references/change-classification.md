# Change Classification Guide

Expanded guidance for classifying codebase changes by their impact on each context document type. Use this to decide which documents need review and what kind of drift to look for.

## Classification Matrix

### Changes That Affect CLAUDE.md

| Change | Impact | What to Check |
|---|---|---|
| New command added to package.json | High | Commands section needs the new entry |
| New path alias in tsconfig.json | High | Path Aliases table needs updating |
| New plugin or agent created | High | Specialized Agents section needs the entry |
| New skill created | Medium | May need documenting if user-facing |
| Architecture folder restructure | High | Architecture section needs updating |
| New dependency added | Low | Tech Stack section, only if it's a major addition |
| Configuration change | Low | Usually no CLAUDE.md impact |

### Changes That Affect Architecture Docs

| Change | Impact | What to Check |
|---|---|---|
| New feature directory created | High | Feature list needs updating |
| New bounded context added | High | Backend architecture section needs updating |
| Layer responsibility changed | High | Layer descriptions need correction |
| New shared component promoted | Medium | Shared code section may need updating |
| File naming convention changed | Medium | Naming conventions need updating |
| Internal refactor (same structure) | Low | Usually no doc impact |

### Changes That Affect Domain Model Docs

| Change | Impact | What to Check |
|---|---|---|
| New entity or aggregate root | High | Entity definitions need the new entry |
| New value object | Medium | VO definitions may need updating |
| New domain event | Medium | Event catalog needs updating |
| Relationship between entities changed | High | Relationship diagrams/descriptions need updating |
| Field added to existing entity | Low | Only if it changes the entity's semantic meaning |
| Validation rule changed | Low | Only if it's a domain invariant, not just input validation |

### Changes That Affect Feature Specs

| Change | Impact | What to Check |
|---|---|---|
| New screen or route added | High | Feature should be documented with flows |
| Acceptance criteria met | Medium | Feature status should reflect completion |
| User flow changed | High | Flow description needs correction |
| UI component added to feature | Low | Usually no spec impact unless it changes the flow |
| Bug fix in existing feature | Low | Usually no spec impact |

### Changes That Affect Tech Stack Docs

| Change | Impact | What to Check |
|---|---|---|
| Major dependency added (new framework) | High | Tech stack needs the entry |
| Major version upgrade | Medium | Version numbers need updating |
| Tool replaced by alternative | High | Old tool removed, new one added |
| Dev dependency added | Low | Usually no doc impact |
| Patch version bump | Low | No doc impact |

### Changes That Affect Roadmap/Vision Docs

| Change | Impact | What to Check |
|---|---|---|
| Planned feature implemented | Medium | Status may move from "planned" to "implemented" |
| New strategic direction in code | High | But ASK the user -- don't assume the plan changed |
| Planned approach abandoned | High | ASK the user -- this may be intentional pivot |
| Code contradicts stated vision | High | ASK the user -- likely a misunderstanding, not drift |

## The "Planned vs. Implemented" Decision Tree

```
Does the item exist in the codebase? (search for files, imports, config)
  YES -> Does the document accurately describe it?
    YES -> No drift. Skip.
    NO  -> This IS drift. Propose a correction.
  NO  -> Is the item explicitly labeled "planned", "pending", "future", or "target"?
    YES -> Leave it alone. This is NOT drift.
    NO  -> Infer from context: could this be aspirational/future intent?
           (e.g., mentions a technology, integration, or capability with
           zero trace in the codebase -- no files, no imports, no config)
      LIKELY FUTURE -> Leave it alone. Treat as unlabeled future plan.
      CLEARLY STALE -> The item describes something that WAS present but
                       has been removed or replaced. This IS drift.
                       Propose a correction.
      UNCERTAIN     -> ASK the user. Do not assume.
```

## Severity Levels

Use these to prioritize which proposals to include when the max (5) would be exceeded:

1. **Critical** -- Document says X, code does the opposite. Users will be misled.
2. **Important** -- New capability exists but document doesn't mention it. Users won't discover it.
3. **Minor** -- Small detail is outdated but unlikely to cause confusion. Can wait.
4. **Cosmetic** -- Style, formatting, or wording preferences. **Never propose these.**
