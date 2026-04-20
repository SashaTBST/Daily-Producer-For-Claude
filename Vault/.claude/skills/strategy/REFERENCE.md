# Strategy — Reference

## Strategic Framework

Every entity has four tracked components:

**Goals** — concrete, measurable, time-bound:
- Users/signups: target, current, delta
- Views/impressions: target, current, delta
- Revenue: target, current, delta
- Other (downloads, subscribers, followers — entity-specific)

Goal format in config:
```
GOAL: [what] | TARGET: [number/outcome] | BY: [date or milestone] | STATUS: [on track / stalled / hit]
CURRENT: [latest known figure] | LAST UPDATED: [date]
BLOCKING: [if stalled — what is in the way]
```

**Audience** — who they are, what problems they have, where they spend time, what they trust.

**Acquisition Channels** — all channels ranked by: likely ROI, current status, effort required.

| Channel | Status | Priority |
|---|---|---|
| Organic content | Active / Inactive | High / Med / Low |
| Community presence | Active / Inactive | High / Med / Low |
| Partnerships | Active / Inactive | High / Med / Low |
| SEO / search | Active / Inactive | High / Med / Low |
| Paid | Active / Inactive | High / Med / Low |
| Product-led growth | Active / Inactive | High / Med / Low |
| Events / jams / launches | Active / Inactive | High / Med / Low |

**Performance Feedback** — what is working, what is not, what to double, what to cut.

---

## User Acquisition Funnel

```
AWARENESS     → They discover the entity exists
INTEREST      → They understand why it is relevant to them
CONSIDERATION → They evaluate: is this worth trying?
ACTIVATION    → They sign up / download / buy / follow
RETENTION     → They keep coming back
ADVOCACY      → They tell others
```

Track where the biggest gap is. Push actions there.

---

## New Entity Phase Model

For entities with no acquisition history or early-stage definition:

**Phase 1 — EDUCATE**
Goal: audience understands what this is and why it matters.
Channels: organic content (explain, define, position), community presence (listen first).
Move to Phase 2 when: core value proposition is clear in all content.

**Phase 2 — EASY CONTENT**
Goal: consistent presence with low-friction content. Volume over complexity.
Channels: organic content (short form, repeatable formats), community (participate, don't pitch).
Move to Phase 3 when: a format is working and the pipeline is consistent.

**Phase 3 — EXPAND**
Goal: scale what works. Add channels. Introduce acquisition mechanics.
Channels: open all channels ranked by ROI. Add partnerships, SEO, or paid if warranted.

When entity already has acquisition history — skip directly to the Acquisition Channels table above.

---

## Content as a Tool

Content is not the goal — it's how you put the entity in front of the right audience at scale.

**Detection** — scan every session for:
- Voice notes with audience-relevant ideas
- Daily notes with decisions, insights, observations worth sharing
- Project activity (launches, milestones, problems solved)
- Industry patterns (recurring topics worth owning)

**Three output types:**

Short form (post, quote, caption) — single self-contained idea. Hook → point → optional CTA.
Long form (article, guide) — 400–800+ words. Practical. Triggered when enough depth exists or topic appears 3+ times.
Quote/pull — a strong sentence worth isolating for standalone post or visual.

**Pipeline:** Draft → Review → Approved → Posted → Logged in `[Project] - ContentLog-Staging.md`
Nothing posted without operator approval.

**Quality gate** — before logging as Draft:
→ Does this give the audience something specific and useful?
→ Is the entity a natural presence, not a forced mention?
→ Does this move toward a defined goal?
If no to any: revise or discard.

---

## Strategy Config Creation Protocol

Run with: `/build-config strategy [entity]`
Ask one at a time. Confirm understanding before proceeding.

1. What is this entity? (Business / Brand / Brand Project)
2. What does it do, and for whom?
3. What is the primary goal right now — users, revenue, views, or something else?
4. What is the specific target and timeline for that goal?
5. Who is the primary audience — describe them specifically, not generically.
6. What problem does this entity solve for that audience?
7. Where does that audience spend time online?
8. What acquisition channels are currently active (if any)?
9. What content has worked before? What fell flat?
10. What is the brand voice — how does this entity communicate?
11. What platforms are confirmed for content distribution?
12. What does "a good month" look like — specifically?
13. What is the single biggest obstacle to growth right now?
14. Is there budget for paid acquisition? If so, what range?

After all 14 answered: build Strategy Assistant Config → set first goal → identify highest-priority acquisition action → propose executing it.

---

## ROLES — LOAD TRIGGERS

| Role | File | Load when |
|---|---|---|
| marketing-planner | `roles/marketing-planner.md` | Strategic direction is confirmed and execution detail is needed: content calendar, campaign plan, platform-specific tactics, asset list, distribution checklist. Do NOT activate before direction is set. |

**Protocol:**
1. Signal: `ROLE ACTIVE: [name] — [one-line role]. Restrictions apply.`
2. Read the role file
3. Execute per that role's scope and output format
4. Return: `ROLE COMPLETE: [name] — returning to /strategy`

Marketing-planner requires an active Strategy Assistant Config. If none exists — run `/build-config strategy [entity]` first.