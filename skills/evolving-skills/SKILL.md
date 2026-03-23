---
name: evolving-skills
description: Detects when the project's skill library needs evolution based on meaningful codebase changes. Evaluates whether new skills should be created, existing skills improved, or stale skills retired. Use after completing a significant implementation, when you notice recurring patterns not covered by existing skills, or when asked "should any skills be updated?", "skill audit", "evolve skills", or "skill maintenance". Proposal-only -- never modifies files without explicit user approval.
---

# Skill Evolution

Detects when the project's skill library is stale, missing, or redundant relative to actual codebase practices, and proposes minimal, targeted changes. This skill only proposes -- it never executes changes without explicit user approval.

## Process

Work through the steps below in order.

### 1. Gather Recent Change Context

Understand what has changed recently:

- Run `git log --oneline -20` and `git diff --stat HEAD~5..HEAD`
- Scan modified files for pattern changes: new directories, renamed conventions, new architectural patterns
- Identify the domains and layers touched by recent work

### 2. Classify Change Significance

Not every change warrants skill evolution. Classify each change:

| Change Type | Significance | Example |
|---|---|---|
| New feature module or bounded context | High | New feature directory, new domain module |
| Architectural decision or pattern adoption | High | New layer introduced, new design pattern established |
| Recurring code pattern (3+ instances) | Medium | Repeated hook structure, repeated API integration pattern |
| Style or convention shift | Medium | New naming convention, new import organization pattern |
| Documentation or comment style change | Medium | New JSDoc convention, new README structure |
| Bug fix, small edit, typo correction | Low (ignore) | Single file fix, dependency bump |

**Threshold rule:** Only proceed if at least one Medium or High significance change is detected. If all changes are Low, report "No skill evolution needed" and stop.

### 3. Inventory Existing Skills

Before proposing anything, understand the current skill landscape:

- Glob for all `SKILL.md` files across the project's plugin directories
- Read each skill's `name` and `description` frontmatter
- Build a mental coverage map: what patterns, workflows, and conventions does the skill library already cover?

### 4. Evaluate Three Possible Actions

#### 4a. Should a new skill be created?

All criteria must be true:

- [ ] A clear, repeatable pattern exists (observed 3+ times in the codebase, or is a deliberate architectural decision)
- [ ] No existing skill covers this pattern (even partially)
- [ ] The pattern is project-agnostic (can be described without project-specific names or terminology)
- [ ] The pattern is complex enough to benefit from structured guidance (not just a one-liner rule)
- [ ] The skill would not overlap with official plugins already installed (e.g., `skill-creator` handles skill creation, not detection)

#### 4b. Should an existing skill be improved?

At least one criterion must be true:

- [ ] The skill's guidance contradicts current codebase practice (the code has moved on, the skill hasn't)
- [ ] The skill is missing a pattern that has become standard in the codebase
- [ ] The skill references outdated tools, libraries, versions, or APIs
- [ ] The skill's decision criteria or checklists have gaps that recent work exposed

#### 4c. Should a skill be retired?

At least one criterion must be true:

- [ ] The pattern the skill teaches has been deliberately abandoned
- [ ] Another skill now fully covers its scope (the skills overlap significantly)
- [ ] The skill's domain has been removed from the project entirely

### 5. Draft Proposal

For each recommended action, present a structured proposal:

```
### Proposal: [Create / Improve / Retire] `skill-name`

**Action:** Create new skill / Improve existing skill / Retire stale skill
**Skill:** [name, or proposed name for new skills]
**Evidence:** [Specific files, commits, or patterns that motivated this recommendation]

**Proposed change:**
- [Bullet 1: what exactly would be added, modified, or removed]
- [Bullet 2: ...]
- [Bullet 3: ...]

**Risk:** [What could go wrong if this change is made]
```

For improvements, include a before/after diff showing the specific lines that would change.

### 6. STOP -- Wait for Approval

**CRITICAL: Do not execute any changes.** Do not create files. Do not edit files. Do not invoke other skills.

Present all proposals and explicitly say:

> "Would you like me to proceed with any of these? Please say 'yes' to all, or specify which ones."

Wait for the user's explicit approval before taking any action. If approved, delegate creation to the `skill-creator` skill or make the edit directly, depending on scope.

---

## Guardrails

- **Max 3 proposals per run.** Focus on the highest-impact changes. If more than 3 are warranted, mention the extras briefly and offer to address them in a follow-up.
- **Prefer improving over creating.** Enhancing an existing skill has lower cognitive overhead than adding a new one.
- **Never duplicate scope.** If two skills would cover the same pattern, improve the existing one instead.
- **Never modify without showing the diff.** For improvements, always show the before/after change.
- **Project-agnostic language only.** Use generic terms ("the project's context documents", "the feature directory") -- never reference project-specific names, domains, or business terminology.
- **Respect the skill-creator boundary.** This skill detects the *need* for skill changes. The `skill-creator` skill (or direct editing) handles the *execution*. Do not replicate skill-creator's workflow.
- **No false urgency.** If the evidence is thin or ambiguous, say "monitoring -- no action needed yet" rather than forcing a proposal.

---

## Related Skills

- `meta-evolution:evolving-agents` -- Same evolution pattern, but for agent definitions
- `meta-evolution:context-sync` -- Synchronizes context documents with recent codebase changes
- `skill-creator` (Native) -- Creates and edits skill files (use after this skill identifies the need)
