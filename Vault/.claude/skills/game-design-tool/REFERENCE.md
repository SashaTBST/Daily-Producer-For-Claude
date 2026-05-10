# Game Maker — HatchFox Framework & Config Protocol

## Tool Framework (HatchFox AI Game Maker)

**Tool A — Game Brief Builder** (BUILT)
Structured Markdown config. Input: brief answers. Output: GDD + Game PRD.
Location: `HatchFox Studios - Business/Game-Brief-Creation-Tool-Claude - Brand Project/`
GitHub: SashaTBST/Game-Brief-Creation-Tool-Claude

**Tool B — AI Studio Workflow** (IN BUILD — 5/14 roles complete)
Folder of role briefs for every game studio position.
Each role: Option A (full AI replacement) + Option B (AI within human workflow).
Roles complete: Gameplay, Blueprints UE5, Animation, Concept Art, Environment
Roles remaining: Skyboxes, Weapon Systems, Door Systems, Multiplayer, References, Audio, Narrative, UI/UX, QA, Production
Location: `HatchFox Studios - Business/AI-Game-Studio-Workflow-Claude - Brand Project/roles/`
GitHub: SashaTBST/AI-Game-Studio-Workflow-Claude

**Tool C — Game Config Library** (QUEUED)
Library of Markdown config files per dimension: art style, audio style, output controls.
Used to generate web-friendly games in Claude Code or any AI system.

---

## 14-Question Config Creation Protocol

Run when a project has no Game Assistant Config. Ask one at a time.

1. What type of game? (genre, platform, target player)
2. What is the core game loop? (the single action the player repeats)
3. What engine or tech stack? (UE5, Unity, web, other)
4. What is the art style? (reference points)
5. What is the audio style? (reference points)
6. What is the narrative, if any? (optional — describe or "none")
7. What is the scope? (jam game / vertical slice / full game)
8. What team exists? (solo / small team / studio — roles available)
9. What is the primary constraint? (time / budget / team size)
10. What already exists? (prototype, GDD, concepts)
11. Which Tool B roles are highest priority for this project?
12. What does the AI replace vs support in this workflow?
13. What is the first deliverable needed from the AI system?
14. What would make this project a success? (measurable)

Output: `[Project]/Working Files/AI/[Project] - Game Assistant Config.md` — push to staging first.
