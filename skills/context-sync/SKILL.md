---
name: context-sync
description: Synchronizes project context documents (CLAUDE.md, architecture docs, domain models, feature specs, roadmaps, and other explanation files) with recent codebase changes. Triggered by typing "/context-sync" or asking to "sync context", "update context docs", "are my docs up to date", or "context synchronization". Also use after completing a significant implementation session, shipping a feature, or making architectural changes. Proposes careful, mostly additive updates and waits for explicit approval. Respects future plans -- never flags unimplemented roadmap items as drift.
---

# Context Synchronization

Audits the project's context documents against recent codebase changes and proposes targeted, mostly additive updates. Never modifies files without explicit user approval.

This skill complements `revise-claude-md` (which captures session-level learnings for CLAUDE.md) by covering the broader set of context documents: architecture docs, domain models, feature specs, roadmaps, and any other explanation files.

## Process

Work through the steps below in order.

### 1. Scan Recent Changes

Gather a picture of what has changed:

- Run `git status` to see staged and unstaged changes
- Run `git log --oneline -15` to see recent commits
- Run `git diff --stat HEAD~10..HEAD` for a structural overview of what files changed
- Identify which domains, features, and architectural layers were touched

### 2. Inventory Context Documents

Read the current state of each context document that exists in the project. Typical documents include:

| Document Type | Covers | Drift Risk |
|---|---|---|
| CLAUDE.md | Commands, architecture overview, guidelines, agent/skill registry | High |
| Architecture docs | Folder structures, layer responsibilities, patterns | Medium |
| Domain model docs | Entities, value objects, events, relationships | Medium |
| Feature specs | User flows, acceptance criteria, feature mechanics | High |
| Tech stack docs | Approved technologies, versions, tooling | Low |
| Vision/strategy docs | Mission, problem statement, solution thesis | Low |
| Roadmap docs | Planned future work, priorities, timelines | Medium |
| AI/ML docs | Agent specifications, prompt design, safety rules | Medium |
| Brand docs | Brand identity, values, voice guidelines | Low |

For each document, mark it as **"needs review"** (recent changes touch its domain) or **"likely current"** (no relevant recent changes).

### 3. Separate Current State from Future Plans

**This step is critical.** Context documents describe both current reality and future plans.

Rules:
- **Planned items that aren't implemented yet are NOT drift.** A document saying "PostgreSQL is planned" when the code still uses in-memory storage is correct -- it describes intent, not a stale claim.
- **Future plans are often unlabeled.** Documents will NOT always mark items with "planned", "pending", or "future". You must infer intent from context. If a document mentions a capability (e.g., "Integrate database", "Pub/Sub event propagation", "vector search via Pinecone") but the codebase has no corresponding implementation, that is almost certainly a future plan -- not an error in the documentation. Cross-reference the codebase: if the code has no trace of the feature (no files, no imports, no configuration), treat the documented item as aspirational intent.
- **Only flag genuine drift:** (a) an implemented feature or pattern that contradicts what the document says, or (b) something new that was implemented but the document doesn't mention at all.
- **If a future plan appears to have changed direction** (e.g., the code evolved toward a different approach than what was planned), do NOT silently update the document. Instead, pause and ask the user:

> "I noticed [X was planned in document Y], but recent changes suggest [Z was implemented instead]. Has the plan changed, or is this a temporary divergence?"

Let the user decide whether the plan should be updated. Future plans take precedence over code -- the code may simply not have caught up yet.

### 4. Detect Drift

For each document marked "needs review", compare what the document says against what the codebase shows:

**CLAUDE.md specific checks:**
- Are all listed commands still accurate?
- Are all listed path aliases still valid?
- Are all listed agents still present? Any new agents missing from the registry?
- Are all listed skills still present? Any new skills missing?
- Does the architecture description match the actual folder structure?
- Does the tech stack section reflect actual dependencies?
- Are workflow descriptions still accurate?

**Architecture doc checks:**
- Are there new directories or modules not mentioned?
- Have any documented patterns been replaced by different ones?
- Are layer responsibilities still accurately described?
- Are implemented vs. planned states correctly labeled?

**Domain model doc checks:**
- Are there new entities or value objects in the code not in the docs?
- Have any entity relationships changed?
- Are there new domain events not documented?
- Have aggregate boundaries shifted?

**Feature spec checks:**
- Are there new routes or screens not in the feature list?
- Have acceptance criteria been met for documented features (update status)?
- Are there entirely new features not yet documented?

**Roadmap/vision/brand checks:**
- Only flag if something in the code explicitly contradicts a stated direction
- Never propose changes to aspirational or forward-looking content based solely on current code state

### 5. Draft Update Proposals

For each document needing updates, present:

```
### Update: [document path]

**Trigger:** [which recent commit or file change motivated this]

**Current state:** [brief quote of what the doc says now]

**Proposed change:**
```diff
+ [additions]
- [removals, if any]
```

**Why:** [1 sentence explaining why this matters for future sessions]

**Rules for proposals:**
- **Precise and targeted.** Change only what has drifted. No broad section rewrites.
- **Prefer additive updates** (adding new info) over removals.
- **Never remove information** unless it is demonstrably wrong (not just incomplete).
- **Link each proposal to a specific recent change** (commit hash or file path).
- **Never propose cosmetic or style-only changes** to context documents.
- **Max 5 proposals per run.** Focus on the highest-impact drift. If more is needed, mention extras briefly and offer a follow-up.

### 6. STOP -- Wait for Approval

**CRITICAL: Do not execute any changes.** Do not edit files. Do not create files.

Present all proposals together and explicitly say:

> "Would you like me to apply all of these, or select specific ones? Say 'yes' to all, or list the ones you'd like applied."

Wait for the user's explicit approval before modifying any file.

### 7. Suggest Follow-Up (Lightweight Coordination)

After context sync is complete, check whether the changes also warrant running the evolution skills:

- If **new patterns or conventions emerged** that might need skill coverage, mention: "The recent changes introduced [X]. You may want to run `evolving-skills` to check if any skills need updating."
- If **new domains or architectural shifts occurred** that might affect agent definitions, mention: "The architectural changes around [Y] may warrant running `evolving-agents` to check if agent definitions are still accurate."

Do not auto-trigger these skills. Just mention them as suggestions.

---

## Guardrails

- **Never modify files without showing the exact diff first.**
- **Never propose changes to documents whose domain was not touched by recent changes.**
- **Never rewrite entire sections.** Propose line-level or paragraph-level edits only.
- **Future plans take precedence.** If something is documented as "planned" or "pending" and isn't implemented yet, that is correct -- not drift.
- **When plans appear to have shifted, ASK.** Do not assume the plan changed. The user decides.
- **If no drift is detected, say so and stop.** "All context documents appear current. No updates needed."
- **Respect `revise-claude-md`'s territory.** This skill covers the full set of context documents. For session-specific learnings that should be captured in CLAUDE.md, the user should use `revise-claude-md` instead.

---

## Related Skills

- `evolving-skills` -- Detects when skills need creating, improving, or retiring
- `evolving-agents` -- Detects when agents need creating, improving, or retiring
- `revise-claude-md` (Native) -- Captures session-level learnings in CLAUDE.md (narrower scope, different trigger)
