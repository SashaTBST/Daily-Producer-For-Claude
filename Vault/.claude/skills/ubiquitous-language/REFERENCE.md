# Ubiquitous Language — Reference

## Naming Rules

1. One term per concept. No synonyms in official documents.
2. If two people use different words for the same thing, pick one and enforce it.
3. Names should be specific enough that they couldn't describe anything else.
4. Prefer nouns for entities, verbs for actions, adjectives for states.
5. Never name something after its container (`utils`, `helpers`, `misc`) — name it after what it does.

## Term Registry Template

```markdown
## Term Registry

| Term | Means | Use instead of | Never confuse with |
|---|---|---|---|
| Staging | Working draft before review | Draft, WIP, in-progress | Prelive (reviewed, awaiting approval) |
| Prelive | Reviewed, awaiting go-live approval | Pre-production, ready | Staging (not yet reviewed) |
| Live | Current authoritative version | Published, production | Staging or Prelive |
| Source of Truth | Canonical reference for a project | Master doc, bible | Working file |
| Vertical slice | End-to-end implementation of one behaviour | Story, task, chunk | Layer (horizontal cut through one technical layer only) |
```

## System-Level Terms (apply across all projects)

| Term | Means |
|---|---|
| **Staging** | Working draft — not reviewed |
| **Prelive** | Reviewed, not yet approved for live |
| **Live** | Authoritative, approved version |
| **Source of Truth** | Canonical reference document for a project |
| **Vertical slice** | Thin end-to-end path through all layers |
| **Tracer bullet** | First vertical slice — proves the path works |
| **HITL** | Human In The Loop — decision requires human |
| **AFK** | Autonomous — no human input required |
| **Deep module** | Small interface + large implementation |
| **Phase** | One of the 7 stages of the development process |

## Creative Exception

Output skills (writer, content) suspend terminology inside generated prose, dialogue, and creative copy.
All user-facing replies and skill instructions always use correct terms.