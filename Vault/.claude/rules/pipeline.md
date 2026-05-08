# Pipeline Rules

## File Lifecycle — Non-Negotiable

STAGING → PRELIVE → LIVE. No exceptions.

- **STAGING** `[ProjectName]-[FileName]-Staging` — all drafts and work-in-progress. Never deleted without explicit instruction.
- **PRELIVE** `[ProjectName]-[FileName]-Prelive` — push request. Operator reviews before promotion. Not modified in place — replaced with a clean push from staging.
- **LIVE** `[ProjectName]-[FileName].md` (no suffix) — authoritative, published version. STRICT GATE. Canonical form is **no suffix**. `-Live.md` is legacy-valid but not used for new files. If both a no-suffix and `-Live.md` version exist in the same location: flag as conflict, do not proceed until resolved (keep no-suffix, archive `-Live.md`).

Never write directly to a live file unless given a live-gate phrase: "push to live", "make it live", "promote to live".
Never skip staging for new working content.
Never auto-promote. Always confirm.

## Live-First Rule — Non-Negotiable

Before any working session on a file:
1. Load the live version first. Confirm it is the source of truth.
2. Ask: "What version are we working from, and is there anything to check or update before we start?"
3. Confirm the correct information from live is loaded before touching staging.
4. If no live file exists: state that and proceed to staging directly.

## Pipeline Data Flow — Non-Negotiable

- LIVE is the source of truth. Never treat staging or prelive as more current.
- Data flows one way: Staging → Prelive → Live. Never backward automatically.
- When pulling from live back into staging — ask for permission first. Never overwrite staging silently.
- If live has edits not in staging/prelive: flag the conflict, state what differs, request permission to sync.
- If a staging change conflicts with live: push back before proceeding. State the conflict clearly.
- After any live edit: check if staging/prelive are out of sync and flag it.

## Prelive Checklist Rule — Non-Negotiable

When pushing any file to prelive:
- Include a specific checklist of every component and line item the operator should review.
- Not "review the content" — name each item explicitly: check [item 1], [item 2], [item N].
- Feedback on prelive = improvement opportunity. If unsure whether it's a tweak or a core change — ask.
