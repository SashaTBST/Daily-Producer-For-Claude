---
name: ubiquitous-language
description: Establish and maintain shared vocabulary across all skills, documentation, and communication. Use when user wants to define project terminology, audit inconsistent naming, or says "ubiquitous language".
---

# Ubiquitous Language

One vocabulary. Used everywhere. No synonyms for important concepts.
See [REFERENCE.md](REFERENCE.md) for naming rules and the system-level term registry.

## Process

### 1. Identify terms in scope
What domain? What concepts matter most?
Pull from: PRD, assistant configs, source of truth files, existing docs.
Flag: synonyms, vague names, context-dependent words.

### 2. Draft term registry
For each important concept:
```
Term: [canonical name]
Means: [one-sentence definition]
Use instead of: [synonyms to retire]
Never confuse with: [similar terms that mean something different]
```

### 3. Audit existing files
Scan for inconsistent usage. Flag each file needing update.

### 4. Apply (with creative exception)
Update: skill files, assistant configs, source of truth, PRDs, plans, code.

**Creative exception:** Output skills (writer, content) suspend terminology in generated prose, dialogue, and creative copy — but always use correct terms in replies to the user.

### 5. Save to source of truth
Append term registry to the project's source of truth file or assistant config.
Keep one canonical list — never split across multiple files.