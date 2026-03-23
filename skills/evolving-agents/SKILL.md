---
name: evolving-agents
description: Detects when the project's agent definitions need evolution based on meaningful codebase changes. Evaluates whether new agents should be created, existing agents improved, or stale agents retired/merged. Use after significant architectural changes, when new domains emerge, or when asked "are agents up to date?", "agent audit", "evolve agents", or "agent review". Proposal-only -- never modifies files without explicit user approval.
---

# Agent Evolution

Detects when agent definitions are stale, missing, or have overlapping responsibilities relative to actual codebase practices, and proposes minimal, targeted changes. This skill only proposes -- it never executes changes without explicit user approval.

Agents encode architectural philosophy and domain expertise. They should be more stable than skills -- only evolve them when the shift is structural, not cosmetic.

## Process

Work through the steps below in order.

### 1. Gather Recent Change Context

Understand what has changed recently, with an agent-specific lens:

- Run `git log --oneline -20` and `git diff --stat HEAD~5..HEAD`
- Focus on changes to:
  - **Architectural patterns** (agents encode how architecture should look)
  - **New domains or bounded contexts** (may need a new specialist agent)
  - **Tooling or technology changes** (agents reference specific tools and workflows)
  - **Quality standards or evaluation criteria** (agents with scoring rubrics may need updates)

### 2. Classify Change Significance

| Change Type | Significance | Example |
|---|---|---|
| New domain or bounded context | High | New backend module, new feature area |
| Architectural pattern change | High | New layer, pattern replacement, new design principle |
| Technology or tooling change | Medium | New library adopted, tool deprecated, API version change |
| Quality standard shift | Medium | New code review criteria, updated scoring dimensions |
| Small code fix, dependency bump | Low (ignore) | Patch update, typo fix |

**Threshold rule:** Only proceed if at least one Medium or High significance change is detected. If all changes are Low, report "No agent evolution needed" and stop.

### 3. Inventory Existing Agents

Before proposing anything, understand the current agent landscape:

- Glob for all agent `.md` files across the project's plugin directories
- Read each agent's `name`, `description`, and skim the body for key responsibilities and boundaries
- Map agent coverage: which domains, concerns, and quality dimensions does each agent own?
- Note any "What You Do NOT Do" boundaries

### 4. Evaluate Three Possible Actions

#### 4a. Should a new agent be created?

All criteria must be true:

- [ ] A distinct concern area has emerged that no existing agent covers
- [ ] The concern is complex enough to warrant a dedicated persona with principles and judgment (not just a checklist -- that would be a skill)
- [ ] The new agent would not overlap more than 30% with any existing agent's responsibilities
- [ ] The agent can be described in project-agnostic terms

#### 4b. Should an existing agent be improved?

At least one criterion must be true:

- [ ] The agent's expertise description references outdated patterns or technologies
- [ ] The agent's scoring rubric (if any) does not reflect current quality standards
- [ ] The agent's principles or philosophy conflict with recent architectural decisions
- [ ] The agent's "What You Do NOT Do" section is incomplete given new capabilities or scope changes
- [ ] The agent's output format or communication style is misaligned with current project conventions

#### 4c. Should an agent be retired or merged?

At least one criterion must be true:

- [ ] Two agents have more than 50% overlap in responsibilities
- [ ] The domain the agent covers has been removed from the project
- [ ] The agent's specialty has been fully absorbed into a more general agent
- [ ] The agent has not been relevant to any work in the project for an extended period

### 5. Draft Proposal

For each recommended action, present a structured proposal:

```
### Proposal: [Create / Improve / Retire / Merge] `agent-name`

**Action:** Create new agent / Improve existing agent / Retire stale agent / Merge overlapping agents
**Agent:** [name, or proposed name for new agents]
**Plugin:** [which plugin it belongs to, or proposed plugin for new agents]
**Evidence:** [Specific files, commits, or architectural shifts that motivated this recommendation]

**Proposed change:**
- [Bullet 1: what exactly would change]
- [Bullet 2: ...]

**Sections affected:** [Which parts of the agent definition change: persona, principles, evaluation framework, boundaries, output format]

**Risk:** [What could go wrong if this change is made]
```

For improvements, include a before/after diff of the specific sections that would change.

### 6. STOP -- Wait for Approval

**CRITICAL: Do not execute any changes.** Do not create files. Do not edit files.

Present all proposals and explicitly say:

> "Would you like me to proceed with any of these? Please say 'yes' to all, or specify which ones."

Wait for the user's explicit approval before taking any action.

---

## Guardrails

- **Max 2 proposals per run.** Agent changes are high-impact (they affect how Claude approaches entire domains). Keep proposals focused.
- **Prefer merging over creating.** Fewer, more capable agents beat many narrow ones.
- **Personas should be stable.** Only propose persona/identity changes for genuinely significant shifts, not small adjustments. Agent voice and philosophy are expensive to change.
- **Never remove without checking usage.** Before proposing retirement, verify the agent hasn't been referenced or invoked in recent sessions.
- **Never modify without showing the diff.** For improvements, always show the before/after change for each affected section.
- **Project-agnostic language only.** Use generic terms -- never reference project-specific names, domains, or business terminology in the proposals themselves.
- **Respect agent boundaries.** If an agent explicitly says it doesn't handle something, don't expand its scope without strong justification.
- **No false urgency.** If the evidence is thin or ambiguous, say "monitoring -- no action needed yet" rather than forcing a proposal.

---

## Related Skills

- `evolving-skills` -- Same evolution pattern, but for skill definitions
- `context-sync` -- Synchronizes context documents with recent codebase changes
