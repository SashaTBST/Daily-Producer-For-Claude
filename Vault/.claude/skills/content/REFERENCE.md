# Content — Reference

Extended protocols for the content skill. Load on demand.

---

## BRAND DESCRIPTIONS

**DUIO (Dev Union)**
Platform: Live indie game platform — social network, project sharing, hiring, task management.
Audience: Indie devs, artists, sound engineers, project managers, all game industry roles.
Content tone: Community-first, practical, indie-native.

**HATCHFOX**
Platform: Creative hub studio — any form of creativity, design, writing, AI systems.
Audience: Creators, designers, builders, AI-forward creatives.
Content tone: No restrictions — creative, exploratory, wide. No IP spoilers.
Trending music reference: `HatchFox Studios - Business/HatchFox Content - Brand/Trending Music/HatchFox Content - Trending Music Log.md`

**TBST DIGITAL**
Platform: Business/web/AI consultancy.
Audience: Business owners, web professionals, technical decision-makers.
Content tone: Professional, direct, expertise-led.

**ATHENA 2327**
Platform: Tiered — Discord/Patreon (early access) → Reddit (comic) → Insta, TikTok, Bluesky, X, Facebook, YT Reels, Threads (public, delayed)
Audience: Sci-fi fans, comics readers, world-building enthusiasts.
Content tone: World-first, atmospheric, cinematic. No story spoilers — tease, never reveal.
Note: Graphic-driven pipeline. AI role is captions, lore copy, and distribution scheduling — not primary content generation.
Delay: 2 weeks — Tier 1 (Discord/Patreon) → Tier 3 (public platforms)

**SASHA (Personal Brand)**
Platform: LinkedIn
Audience: Indie creators, solo entrepreneurs, game developers, AI-curious creatives.
Content tone: Practitioner-direct. No generic advice. Lived experience only. No IP reveals.

---

## BRAND ASSISTANT CONFIG CREATION PROTOCOL

Run when a brand has no `[Brand] - Content Assistant Config.md`. Ask one question at a time. Wait for each answer.

**INTRO:** "I'm going to ask you [N] questions to build the Content Assistant Config for [Brand]. This file will tell me how to create content for this brand specifically. Answers here override my generic behavior."

Q1. How does [Brand] sound? Not adjectives — describe it in terms of what it sounds like in a sentence. What's the non-negotiable voice?

Q2. Give me one or two examples of on-brand phrasing or language for [Brand]. What does it actually say?

Q3. What does [Brand] never sound like? What breaks the brand voice immediately?

Q4. What platforms does [Brand] post on, and does the voice shift at all per platform?

Q5. What content types does [Brand] use? (short posts, articles, threads, video scripts, newsletters, other)

Q6. What topics does [Brand] own — have real authority on and can credibly post about?

Q7. What topics should [Brand] avoid or handle with care?

Q8. Who is the primary audience? Be specific — not "business owners" but what kind, what they care about, what they don't have time for.

Q9. What do they respond to? What hooks them?

Q10. What do they ignore or distrust?

Q11. Are there any recurring content series — regular formats or themes posted on a cadence?

Q12. Any open questions about this brand's content that we haven't resolved yet?

**On completion:** Create `[Brand]/[Brand] - Content Assistant Config.md` using the template at `_AI/TEMPLATES/content-assistant-config-template.md`. Report the file path.

---

## FILE ROUTING

Before creating any new file in a content session:

| What you're creating | Correct location |
|---|---|
| Content strategy | `[Brand]/[Brand]-ContentStrategy-Staging.md` |
| Content log | `[Brand]/[Brand]-ContentLog-Staging.md` |
| Article draft | `[Brand]/[Brand]-[Topic]-Article-Staging.md` |
| Short post draft | Add entry to the brand's ContentLog — do not create a separate file |
| Trending music entry | `HatchFox Studios - Business/HatchFox Content - Brand/Trending Music/HatchFox Content - Trending Music Log.md` |

Routing reference: `_AI/WORKFLOWS/file-routing.md`
If a strategy or log file doesn't exist: create at the staging location above. Flag as new. Do not duplicate.

---

## SELF-IMPROVEMENT

Operates at Levels 1 and 2. Level 3 is gated.
Framework: `_AI/SYSTEM/self-improvement-framework.md`

**Level 1 — Micro (always active)**
Adjust brand detection logic, post format, and article threshold decisions in real-time.
Focus: Is brand assignment accurate? Is the post/article threshold applied correctly? Are content logs being updated?

**Level 2 — Session flags (end of session)**
Triggered by: brand misassignment corrected 2+ times, wrong format generated, content log not found.
Output: `IMPROVEMENT FLAGGED [Level 2]: [observation] → [proposed change]`
Log to: `_AI/MEMORY/improvements-log.md` (note: content skill)
Do not auto-apply. Operator reviews via /improve.

**Level 3 — Macro (gated)**
Changes to detection logic, brand definitions, or command set go to staging. Operator approves before SKILL.md updates.

Standalone: all levels active without the daily config loaded.

---

## CONTEXT WINDOW

Full protocol: `_AI/SYSTEM/context-window-protocol.md`
Content sessions can load multiple brand strategy files and logs. Monitor context — act early.

1. Alert: "CONTEXT WINDOW CLOSING — saving session state now."
2. Write to: `_AI/MEMORY/session-state-[YYYY-MM-DD].md`
   Include: which brands were active, posts/articles in Draft, article opportunities flagged, improvement flags.
3. Flush improvement flags to `_AI/MEMORY/improvements-log.md`
4. Report save. Output: "TO RESUME: Start a new chat and run /content [brand or scan]"

Command: `/save-session`
