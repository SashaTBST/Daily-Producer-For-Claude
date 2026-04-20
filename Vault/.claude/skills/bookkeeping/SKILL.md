---
name: bookkeeping
description: Australian sole trader bookkeeping assistant. Accepts raw financial data and formats into clean accountant-ready records. Tracks sole trader business income/expenses and employment D-item deductions separately. Use when logging income or expenses, generating FY summaries, formatting receipts, or preparing accountant handoff materials.
argument-hint: "Optional: FY year (e.g. FY2025), workflow (log/report/receipt/review/export), or a date range"
---

# Bookkeeping

Australian FY: **1 July → 30 June**. Not for filing taxes — for reducing accountant data entry.
Tracks: [Business] sole trader income/expenses + [Employer] employment D-items + PAYG/partner income references.

---

## First-run setup

Before the first LOG session, collect:
1. ABN
2. Trading name — business name or personal name on invoices?
3. Home office method: A) Fixed rate (70c/hr) or B) Actual cost (m²) — see REFERENCE.md
4. Vehicle: A) Cents per km (88c/km, max 5,000km) or B) Logbook method — see REFERENCE.md
5. Starting FY — if starting with historical data, creates FY2025 + FY2026 ledger files simultaneously

Store setup answers in the ledger file header. Do not re-ask once set.

---

## Workflows

### LOG — Add income or expense entries
Accept raw input in any format. Extract: date, type, source/vendor, description, amount (inc. GST), GST, category, entity ([Business] / [Employer] D-item), receipt status.
Mixed-use items: ask for [Business] % / [Employer] % / Personal % split.
Show parsed entries as a confirmation table. Write to ledger only on confirmation.

### REPORT — FY or date range summary
Income, expenses by category, GST position, net. Split: [Business] vs [Employer] D-items.
Accepts: `FY2025`, `FY2026`, `Q1 FY2026`, custom date range.

### RECEIPT — Format one transaction
Outputs a single clean receipt record with all required fields.

### REVIEW — Audit a period
List all entries. Flag: missing receipts, uncategorised items, duplicates, entries outside selected FY.

### EXPORT — Accountant handoff
Prompts for: PAYG employment income amount (from MyGov) + partner income amount.
Produces: [Business] business summary + [Employer] D-items summary + flagged items + PAYG/partner notes.
Writes to export file. Output is copy-paste ready for email.

---

## Constraints

- AUS FY only: 1 July → 30 June. Flag any entry outside selected FY.
- GST is 10%. GST-free supplies noted explicitly.
- Never guess category or entity — ask when ambiguous.
- Never calculate tax liability. State amounts and categories only.
- All amounts AUD. Records kept 5 years minimum.
- Instant asset write-off: $20,000/asset until 30 June 2026, then reverts to $1,000. Flag all assets.
- Meals/entertainment: flag ALL — do not auto-apply 50% rule. Accountant assesses.
- Overseas subscriptions (Adobe, Google etc): flag for reverse-charge GST review.

---

## Files

- Ledger: `[Person] - Person/Bookkeeping/[Person] - Bookkeeping-[FY]-Staging.md`
- Export: `[Person] - Person/Bookkeeping/[Person] - Bookkeeping-[FY]-Export-Staging.md`
- Reference: `REFERENCE.md` (categories, formats, AUS rules, prompts)
