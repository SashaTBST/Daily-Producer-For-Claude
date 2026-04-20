---
name: improve-codebase-architecture
description: Audit and improve architecture to make it AI-navigable. Identifies shallow modules, missing ubiquitous language, and surface-area problems. Use when user wants to make code AI-ready, refactor for clarity, or says "improve the architecture".
---

# Improve Codebase Architecture

Make code or vault structure AI-navigable. Focus: deep modules, clear interfaces, ubiquitous language.
See [REFERENCE.md](REFERENCE.md) for deep module patterns, AI-readiness checklist, and anti-patterns.

## Process

### 1. Audit
- Read entry points and public interfaces
- Identify shallow modules (large interface, thin logic)
- Identify missing or inconsistent terminology (see /ubiquitous-language)
- Identify hidden dependencies and tangled modules

### 2. Report
Present findings:
- **Red flags** — shallow modules, inconsistent naming, tangled deps
- **Quick wins** — renames, extractions, interface simplifications
- **Deep refactors** — structural changes, module boundary shifts

Get approval before changing anything.

### 3. Refactor (one change at a time)
For each approved change:
- Apply refactor
- Confirm behaviour preserved (run tests if available)
- Document what changed and why

### Rules
- Never refactor while tests are RED
- One refactor at a time
- Prefer deepening modules over splitting them
- Apply /ubiquitous-language after architecture is stable
