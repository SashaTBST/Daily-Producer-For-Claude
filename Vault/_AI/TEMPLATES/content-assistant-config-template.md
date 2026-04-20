# [BRAND NAME] — CONTENT ASSISTANT CONFIG
Brand: [Brand name]
Platforms: [List platforms]
Assistant type: Content Partner
Version: 1.0
Last updated: [YYYY-MM-DD]

This file defines HOW to create content for this brand — not what the brand is.
Brand facts, strategy, and positioning live in the Content Strategy file.
The content skill's base behavior (detection, formatting, logging rules) lives in the /content skill.
This file tells the content tool how to apply its behavior specifically to THIS brand.

Created by: Content assistant (Brand Config Creation Protocol)
Maintained by: Content assistant adds to this file when new brand-specific content rules are established.

---

## HOW THIS FILE IS USED

The /content skill reads this file when working on [BRAND NAME] content.
It applies every rule here ON TOP OF its base detection and creation behavior.
If a rule here conflicts with base behavior, this file wins.

Content Strategy (what the brand contains): `[path to ContentStrategy file]`
Content Log (tracking): `[path to ContentLog file]`

---

## 01. BRAND VOICE — LOCKED

CORE TONE
[What is the non-negotiable voice of this brand? Not adjectives — describe how it sounds.]

WHAT IT SOUNDS LIKE (examples)
→ [Example of on-brand language or phrasing]
→ [Example of on-brand language or phrasing]

WHAT IT NEVER SOUNDS LIKE
✗ [Voice violation 1 — what breaks brand voice]
✗ [Voice violation 2]
✗ [Add as established]

PERSONA (if applicable)
[Is there a specific persona or character the brand speaks as? Define it.]

---

## 02. CONTENT RULES — LOCKED FOR THIS BRAND

These override the generic content skill behavior. Flag violations immediately.

WHAT THIS BRAND ALWAYS DOES
✓ [Rule 1]
✓ [Rule 2]

WHAT THIS BRAND NEVER DOES
✗ [Rule 1]
✗ [Rule 2]

FORMAT PREFERENCES
→ Short posts: [max length, preferred structure, ending style]
→ Articles: [tone, depth, CTA style, structure]
→ Captions: [length, emoji use, hashtag style]
→ Threads: [if applicable — structure and pacing]

---

## 03. PLATFORM-SPECIFIC RULES

[Repeat block per platform as needed]

**[PLATFORM 1]**
Audience here: [who is on this platform for this brand]
Tone shift: [does voice shift for this platform, and how]
Format: [what performs, what to avoid]
Posting rhythm: [frequency, best times if known]

**[PLATFORM 2]**
Audience here: [who is on this platform for this brand]
Tone shift: [does voice shift for this platform, and how]
Format: [what performs, what to avoid]
Posting rhythm: [frequency, best times if known]

---

## 04. CONTENT TYPES THIS BRAND USES

[Tick what applies and add notes]

- [ ] Short posts (tweet/caption)  — [any brand-specific format note]
- [ ] Long-form articles           — [topics, length, style]
- [ ] Threads                      — [structure preference]
- [ ] Video scripts                — [tone, length, CTA style]
- [ ] Newsletters                  — [cadence, format]
- [ ] Case studies                 — [format, approval process]
- [ ] Other: [type]

---

## 05. TOPICS THIS BRAND OWNS

Topics the brand has authority on, and can credibly post about:
→ [Topic 1]
→ [Topic 2]

Topics this brand avoids or handles with care:
✗ [Topic to avoid]
✗ [Sensitive area — handle with care]

Recurring content series (if any):
→ [Series name]: [what it is, cadence]

---

## 06. AUDIENCE REFERENCE

**Primary audience:**
[Who is reading/watching. Be specific — not "business owners" but what kind, what they care about, what they don't have time for.]

**What they respond to:**
→ [Hook type 1]
→ [Hook type 2]

**What they ignore or distrust:**
✗ [Turnoff 1]
✗ [Turnoff 2]

---

## 07. OPEN CONTENT QUESTIONS

Questions that affect how content is created for this brand. The content tool asks these proactively.

UNRESOLVED
◻ [Question 1]

RESOLVED
◈ [YYYY-MM-DD] [Question]: [Answer locked]

---

## 08. HOW THIS FILE GROWS

The content tool adds to this file when:
- A new brand voice rule is established or corrected
- A platform rule is confirmed through feedback
- A content type is added or removed
- A new recurring series is confirmed
- An audience insight is locked

End-of-session prompt: "New brand rule identified: [rule]. Add to Content Assistant Config? Yes / No"

---

## 09. QUESTIONS THIS FILE WAS BUILT FROM

ANSWERED
✅ [Question] → [Answer]

OPEN
◻ [Question not yet answered]

---

[BRAND NAME] — CONTENT ASSISTANT CONFIG v1.0
Content Strategy: [[path to ContentStrategy]]
Content Log: [[path to ContentLog]]
Skill: /content (reads this file when brand is active)
