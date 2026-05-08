# Build Protocol

Before building any new artifact or significantly expanding scope — run these phases in order.
No jumping straight to staging. No skipping phases.

Applies to: new skills, workflows, templates, system files, assistant configs, PRDs, plans, and any system change affecting more than one file.

---

## INBOUND REQUEST GATE

When any request matches the artifact list above — **STOP before doing anything else.**

Output this block before proceeding:

```
BUILD PROTOCOL TRIGGERED
Artifact type: [what is being built]
Phase: GATHER

[Ask the blocking questions. Do not read files, draft content, or write anything until GATHER is complete.]
```

Do not skip this block. Do not proceed to RESEARCH until GATHER questions are answered **and the operator has explicitly approved moving to the next phase.**
This gate fires even when the request seems simple or well-defined. Complexity is always discovered, never assumed.

**Phase transitions require explicit operator approval. After completing each phase, present a summary and ask:**
```
Phase [N] complete. Ready to move to [NEXT PHASE]?
```
Do not advance until the operator confirms.

---

## 7 Phases

**1. GATHER**
Collect all inputs: existing files, live versions, constraints, dependencies.
Ask what already exists before creating anything new.
Load the live version first (live-first rule applies).

End of GATHER — output this block before asking for approval:
```
GATHER COMPLETE
Entity / scope: [what is being built and for whom]
Constraints confirmed: [key rules, limits, preferences]
Existing files found: [yes/no — what]
Open questions: [any blocking unknowns — if none, say NONE]
Ready to move to RESEARCH?
```

---

**2. RESEARCH**
Three parts — all required:

**Vault research:** Read existing files, live versions, configs, and related skills.

**Prior art check:** Search GitHub and the web for existing implementations before building anything.
Ask: "Has this been built?" Find open-source tools, reference implementations, or prior art to build on rather than from scratch.

**Online research:** Minimum 5 web research points — more if first pass leaves gaps, do not proceed with uncertainty.
Cover: current standards, rates, regulations, best practices, real-world requirements, competitor/alternative landscape.
Do not rely on training data alone. Verify facts that may have changed (rates, thresholds, rules, dates).

**Marketability test — external/commercial ideas only:**
- Is this solving a real, validated problem?
- Does existing demand signal exist?
- Who are the alternatives or competitors?
- What is the commercial or adoption potential?
Internal improvements: skip marketability, but still define what problem is being solved.

Research files are temporary — scoped to the sprint, deleted after.
Do not assert before researching. Do not build from memory alone.

End of RESEARCH — output this block before asking for approval:
```
RESEARCH COMPLETE

Files read (vault): [list]
Prior art found: [existing tools/implementations found — if none, say NONE]
Online research conducted: [N points — topics searched and sources checked]

Relevant findings: [what exists in vault that affects the build]
Corrections to prior assumptions: [anything research changed — if none, say NONE]
Gaps found: [what's missing or needs to be built from scratch]
Conflicts: [anything live that contradicts the request — if none, say NONE]
Marketability / problem validation: [for commercial ideas — or INTERNAL IMPROVEMENT]
Items to verify with source/expert: [facts flagged as uncertain — if none, say NONE]

Ready to move to PROTOTYPE?
```

---

**3. PROTOTYPE**
Draft structure and content. Propose before writing to any file.
Get operator direction on the prototype before staging anything.

End of PROTOTYPE — output this block before asking for approval:
```
PROTOTYPE PROPOSED
Structure: [outline of what will be built — sections, workflows, files]
Key decisions made: [choices that could have gone another way]
What's not included and why: [scope decisions]
Direction needed: [any choices the operator must make — if none, say NONE]
Token cost: [does this load large files every session, or can it reference by path?]
Proceed to GRILL?
```

---

**4. GRILL**
Challenge the prototype. Surface gaps, wrong assumptions, missing pieces.
Ask hard questions. Do not protect the prototype — stress-test it.
Run `/grill-me` if needed.
This is the gate before staging. Do not proceed without passing it.

End of GRILL — output this block before asking for approval:
```
GRILL COMPLETE
Challenges raised: [what was questioned]
Changes made to prototype: [what was revised as a result]
Confirmed as solid: [what held up under scrutiny]
Outstanding risks: [anything unresolved — if none, say NONE]
Approved to STAGE?
```

---

**5. STAGE**
Write to staging file only. Follow file-routing.md for correct location and naming.
No direct-to-live writes. No skipping prelive.

End of STAGE — output this block before asking for approval:
```
STAGE COMPLETE
Files written:
  - [path] — [what it contains]
Pipeline status: Staging only. Not yet in prelive.
Ready to push to PRELIVE?
```

For code or skill builds requiring execution testing: invoke `/tdd` for vertical slice red-green-refactor before pushing to prelive. TDD completes before Phase 6.

---

**6. PRELIVE / LIVE**
Follow standard pipeline rules. Prelive for review. Live only on explicit approval.

---

**7. QA EVIDENCE**
Mandatory after every build. Cannot be skipped. No item is complete without this block written.

Write this block to the **plan file** for the artifact. If no plan file exists: write to `_AI/MEMORY/improvements-log.md`.

```
QA EVIDENCE — [artifact name] — [YYYY-MM-DD]

Output files:
  [x] [path] — [verification tier]
  [x] [path] — [verification tier]

System checks:
  [x] File exists at correct path
  [x] Content matches intent (grep/read confirms)
  [x] Follows format standard (YAML frontmatter / line limit / naming convention)
  [x] No phantom commands or broken refs
  [x] Security surface reviewed — input validation, injection risk (skip: docs/templates/config)

Portable sync:
  [x] [local path] → [portable path] — synced
  — OR — N/A: IP-specific / local-only (reason)

Goal check:
  Problem being solved: [restate it in one line]
  Output solves it: YES / NO — [one line why]

Result: PASS / FAIL
Fail action: [what broke] → [next step] → item unticked and returned to backlog
```

**Verification tiers — use the highest applicable:**
- `exists` — file confirmed present at correct path
- `content-verified` — content read/grepped, matches intent
- `behaviour-tested` — skill invoked or system exercised against live session
- `operator-confirmed` — operator explicitly validated output works as expected

**FAIL rule — non-negotiable:**
Result = FAIL → item immediately unticked in plan file → failure reason noted → do not advance to prelive.
Session resume scans for `Result: FAIL` blocks alongside unchecked `[ ]` items.

**Post-QA cleanup — non-negotiable:**
After `Result: PASS` is written: staging and prelive pipeline files for this build are stale. Do not surface them as operator decisions. Route to `/vault-clean`. The PASS block is the evidence — do not re-verify it.

---

## System Changes — Extra Gate

Any system change affecting more than one file requires a plan file in `plans/` before any files are touched.

The plan file must include:
- What is being changed and why
- List of every file affected
- Completion criteria as checkboxes (`- [ ]`)
- Verification step: how the operator confirms it worked

Session start will surface any plan with incomplete criteria. System changes are not complete until every checkbox is ticked **and** a QA EVIDENCE block is written — not self-reported.

---

## New Concept — Folder-First Rule

When a new concept, idea, or project is approved or begins generating work:

1. **Create the folder first** — before any file, staging doc, or note is written.
2. **Folder naming:** `[ConceptName] - Brand Project/` under the parent business folder, or `Tool-[X]-[Name]/` for tool sub-folders.
3. **Create an overview staging file** inside the folder immediately: `[ConceptName] - Overview-Staging.md` — captures what it is, purpose, status, open questions, and links.
4. **Register the folder** in `_AI/daily-ai-config.md` under the correct project before any further work begins.
5. **If a GitHub repo exists or will be created:** add it to the GITHUB REPOS REGISTRY in the same operation.

**Why:** Concepts without folders become orphaned files, invisible to the daily checker, and disconnected from the project hierarchy.

---

## Personal Requests — System-First Rule

When a personal request arrives (lifestyle plan, bookkeeping, health tracking, personal brand, personal budget, etc.):

1. **Check if a skill exists** for this type of work. If not — build the skill first via `/write-a-skill` before doing the personal work.
2. **Check if a template exists** for the output file type. If not — create it in `_AI/TEMPLATES/` first.
3. **Then** collect personal data and produce the personal output using the skill + template.

**Why:** Personal data is local-only. The skill and template are system assets any user can benefit from. Building the system before adding personal data ensures the framework exists independently — system first, personal data layered on top at the local level.

---

## Non-Negotiable

- Never write a new system file directly to live
- Never skip the Grill phase — this is where gaps get caught before they become logged-as-done-but-not-done
- Never mark a system change complete in the improvements log without verifying the actual files exist and behave as documented
- **Never tick a plan item complete without writing a QA EVIDENCE block — no exceptions**
- **Never skip portable sync — if it isn't in the QA EVIDENCE block, it wasn't done**
