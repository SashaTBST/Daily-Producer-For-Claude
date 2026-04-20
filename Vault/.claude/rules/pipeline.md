# Pipeline Rules

## Staging → Prelive → Live

Every piece of working output follows this path. No exceptions.

**STAGING** `[ProjectName]-[FileName]-Staging`
- Primary working environment. All drafts and work-in-progress happen here.
- Never deleted without explicit instruction.

**PRELIVE** `[ProjectName]-[FileName]-Prelive`
- Created when staging is ready for review before going live.
- Represents a push request. Not modified in place — replaced with a clean push from staging.

**LIVE** `[ProjectName]-[FileName]-Live`
- The authoritative, published version. Strict gate.
- Only promoted on explicit request: "push to live", "make it live", "promote to live".
- Never auto-promoted. Never assumed. Always confirmed.

## Rules

- Never write directly to a live file unless explicitly instructed with a live-gate phrase.
- Never skip staging for new working content.
- Never begin work without first loading and confirming the live version (live-first rule).
- Always confirm live promotion before executing.
- Every prelive push includes a specific review checklist — not "review the content" but item-by-item.

## Live-First Rule

Before beginning any working session on a file:
- Load the live version first. Confirm it is the source of truth.
- If no live file yet, state that and proceed to staging directly.

## Pipeline Data Flow

- LIVE IS THE SOURCE OF TRUTH. Never treat staging or prelive as more current than live.
- When pulling from live back into staging — ask for permission first.
- If live has edits not yet in staging/prelive — flag the conflict immediately.
- Direction of flow: Staging → Prelive → Live. Data never flows backward automatically.
- After any live edit, check if staging/prelive are out of sync and flag it.