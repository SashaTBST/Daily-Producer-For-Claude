◆ LOG CHECK — /log-check ◆
Single-pass sweep of all system logs. Runs automatically via [[_AI/SYSTEM/daily-run]] at session start.
Manual trigger: /log-check

---

LOGS — CHECK IN ORDER

1. SYSTEM IMPROVEMENTS
   File: [[_AI/MEMORY/improvements-log]]
   Look for: entries with STATUS: Staged
   Flag: file affected + change description

2. AI PRODUCER IMPROVEMENTS
   File: [[_AI/MEMORY/improvements-log]]
   Look for: entries tagged [SKILL: ai-producer] with STATUS: Staged
   Flag: suggested fix + section affected

3. ATHENA PIPELINE
   File: [[HatchFox Studios - Business/Athena 2327 - Brand/Sycthe of Athena - Brand Project/Sycthe of Athena - Book-Planning]] → PLANNING PROGRESS TRACKER section
   Look for: unchecked [ ] items that are blocking downstream items
   Flag: blocker name only — skip low-priority open items unless everything above is clear

---

OUTPUT FORMAT

| Log | Entry | Status | Action |
|-----|-------|--------|--------|
| [log name] | [entry summary] | [status] | [action or CLEAR] |

SUMMARY LINE: [X] items need action. [Y] clear.
If all clear: "ALL LOGS CLEAR."

---

RULES
→ Report only what requires action. Do not list clear entries.
→ One row per actionable item. No prose.
→ If a log file does not exist yet, mark it as NOT CREATED — flag for setup if needed.
