---
name: tax-return
description: Australian personal tax return preparation assistant. Collects income, deductions, and investment data across PAYG, sole trader, index funds, and cryptocurrency. Produces an accountant-ready handoff document for a registered tax agent. Use when preparing an Australian personal tax return, gathering FY income and deduction data, or creating a handoff package for lodgment.
argument-hint: "Optional: FY year (e.g. FY2025) or workflow (gather/review/export)"
---

# Tax Return

AUS personal tax — PAYG + sole trader + investments + crypto. Output is agent-ready. Not for self-lodgment.
FY: 1 July → 30 June. Tax agent deadline: 15 May (following year).

---

## First-run setup

Collect before any workflow:
1. FY (e.g. FY2025 = 1 Jul 2024 – 30 Jun 2025)
2. Tax agent name and firm
3. Income streams active this FY: PAYG / Sole Trader / Vanguard / Crypto / Other
4. Partner income: yes/no — if yes, collect taxable income amount (MLS assessment)
5. Private health insurance (hospital cover): yes/no

Store in file header. Do not re-ask.

---

## Workflows

### GATHER — collect all data, stream by stream

Run in order. Each step requires a source document before proceeding.

**Step 1 — PAYG ([Employer])**
Source: ATO income statement via MyGov — prompt to have it open.
Collect: gross income, total tax withheld, reportable fringe benefits, reportable employer super.
Then collect D1–D10 work-related deductions (see REFERENCE.md for categories and rules).
Flag any claim > $300 without a receipt.

**Step 2 — Sole Trader ([Business Name])**
Check if bookkeeping export exists: `[Name] - Person/Bookkeeping/[Name] - Bookkeeping-[FY]-Export-Staging.md`
- If exists → reference it, pull net income/loss.
- If not → collect raw: total income, total deductible expenses by category.
  Flag: "Bookkeeping not complete — figures approximate. Run /bookkeeping before final lodgment."
Confirm: GST registered? BAS lodged for the FY?

**Step 3 — Investment Income (Vanguard)**
Source: Vanguard Annual Tax Statement — prompt to download from Vanguard portal if not on hand.
Collect: unfranked dividends, franked dividends, franking credits, capital gains distributions
(discounted and non-discounted), any units sold (date acquired, date disposed, cost base, proceeds).

**Step 4 — Cryptocurrency (Coinbase)**
Source: Coinbase tax report — prompt to download at Coinbase > Taxes > Reports > select FY period.
Do not proceed without the report. Do not estimate.
For each disposal: date acquired, date disposed, cost base (AUD), proceeds (AUD), gain/loss.
Flag: 50% CGT discount applies if held 12+ months before disposal.
Flag: staking rewards and airdrops = ordinary income, not CGT — collect separately.
If disposal year is uncertain: resolve from Coinbase report only.

**Step 5 — Other Deductions**
Collect: personal super contributions (amount + Notice of Intent lodged with fund: Y/N),
income protection insurance premiums, charitable donations $2+ (DGR only),
prior year tax agent fees, investment income expenses.

**Step 6 — Partner Income + MLS Check**
Collect partner's taxable income.
If combined income > $186,001 and no hospital cover → flag MLS surcharge risk (1%–1.5%).
See REFERENCE.md for MLS thresholds.

---

### REVIEW — audit before export

Run after GATHER is complete:
- Flag any income stream missing its source document
- Flag any deduction > $300 with no receipt recorded
- Flag crypto disposals with unresolved year
- Flag sole trader if bookkeeping export was not used
- Flag if personal super contributions were not made (note missed opportunity)
- Flag MLS exposure if applicable
Output: numbered list of flagged items with specific actions for each.

---

### EXPORT — accountant handoff

Produces: `[Name] - Person/Tax/[Name] - TaxReturn-[FY]-Staging.md`
Uses: `_AI/TEMPLATES/tax-return-template.md`
Copy-paste ready for email to tax agent.
Includes all income, deductions, flags, and documents checklist.

---

## Constraints

- AUS FY only. Never calculate final tax liability — state amounts only.
- Never guess: if data is missing, prompt for the source document.
- All amounts AUD. Sole trader figures GST-exclusive (net of GST claimed via BAS).
- Crypto: every disposal is a CGT event. Uncertain year = flag, do not estimate.
- Super: only claimable if contributed by 30 June AND Notice of Intent lodged before return filed.
- Meals/entertainment: flag all — do not auto-apply the 50% rule. Agent assesses.

---

## Files

- Output: `[Name] - Person/Tax/[Name] - TaxReturn-[FY]-Staging.md`
- Template: `_AI/TEMPLATES/tax-return-template.md`
- Bookkeeping export: `[Name] - Person/Bookkeeping/[Name] - Bookkeeping-[FY]-Export-Staging.md`
- Rate card + D-items + CGT rules: `REFERENCE.md`
