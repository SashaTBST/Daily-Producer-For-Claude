# Changelog

All notable changes to this project will be documented here.
Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)
Versioning: [Semantic Versioning](https://semver.org/)

## [Unreleased]

## [3.2.0] - 2026-04-26

### Added
- 30 skills overhauled to Matt Pocock format standard (YAML frontmatter, QA section, anti-patterns)
- REFERENCE.md overflow files for skills exceeding 100 lines
- Build protocol: 6-phase gate (Gather → Research → Prototype → Grill → Stage → Live)
- New skills: /bookkeeping, /tax-return, /lifestyle-plan, /security-review
- Hooks: PreCompact auto-save, Stop log, UserPromptSubmit date injection, Live file gate

### Changed
- /content deprecated — /strategy supersedes for new entities
- Skill format standardised: YAML frontmatter required, description under 1024 chars

## [3.1.0] - 2026-03-19

### Added
- Strategy skill replaces content skill for new brand entities
- 14-question Strategy Config Creation Protocol
- Sub-skill (role) standard for director skills

### Changed
- daily-ai-config.md updated to v1.6
