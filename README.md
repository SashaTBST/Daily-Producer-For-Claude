# Daily-Producer-For-Claude

> An AI operating system for Claude Code that manages multiple projects simultaneously — writing, content, games, and business.

![Status](https://img.shields.io/badge/status-active-brightgreen)

## What Is Daily-Producer-For-Claude?

One command runs your entire day. This is an AI operating system built on top of Claude Code and Obsidian. It manages multiple projects at once, builds project-specific AI partners, and drives work forward across writing, content, game development, and business — without you having to decide what to do next.

Designed for non-developers and developers alike. No code required to use.

## Requirements

- [Obsidian](https://obsidian.md) — your workspace (free)
- [VS Code](https://code.visualstudio.com) — where you run the AI (free)
- [Claude Code](https://claude.ai/code) — the AI engine (Anthropic subscription or API key required)
- [Node.js](https://nodejs.org) LTS — required by Claude Code
- Git (optional — for vault backup and version control)

## Install

1. Download and open [Obsidian](https://obsidian.md). Create a new vault.
2. Clone or download this repo.
3. Copy the contents of `Vault/` into the root of your Obsidian vault:

```
Your vault root/
├── _AI/        ← copy this in
├── .claude/    ← copy this in
└── CLAUDE.md   ← copy this in
```

4. Open your vault folder in VS Code.
5. Install the [Claude Code extension](https://marketplace.visualstudio.com/items?itemName=Anthropic.claude-code) in VS Code.

See [STARTER-GUIDE.md](STARTER-GUIDE.md) for full setup instructions including Node.js, Git, and first-run configuration.

## Usage

Open your vault in VS Code with Claude Code active, then run:

```
/daily
```

That is the entire daily workflow. The system scans your vault, surfaces open actions across all projects, and proposes the first specific next step.

## How It Works

The system has three layers:

- **Skills** — specialist roles Claude activates on demand (`/strategy`, `/writer`, `/game-maker`, `/tdd`, and more). Each lives in `.claude/skills/[name]/SKILL.md`.
- **Project Assistant Configs** — briefing documents built once per project so Claude knows your specific context without re-prompting.
- **Pipeline** — every output follows Staging → Prelive → Live. Nothing goes live without explicit approval.

For AI-assisted development, it includes a 7-phase build process (Idea → Research → Prototype → PRD → Plan → Execute → QA) and a Ralph loop for autonomous task execution between human checkpoints.

## Contributing

Issues and feedback welcome — open a GitHub issue to report a bug, suggest a skill, or ask a question.

## License

MIT © 2026 SashaTBST
