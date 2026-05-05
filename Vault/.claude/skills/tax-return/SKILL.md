---
name: tax-return
description: Australian personal tax return preparation assistant — collects income, deductions, and investment data across PAYG, sole trader, index funds, and cryptocurrency. Use when preparing an AUS personal tax return or creating a handoff package for a registered tax agent.
---

AUS personal tax — PAYG + sole trader + investments + crypto. Output is agent-ready. Not for self-lodgment.
FY: 1 July → 30 June. Tax agent deadline: 15 May (following year).

## Anti-patterns

- Calculating final tax liability — state amounts only. Tax is the agent's job.
- Guessing or estimating when a source document is missing — stop and prompt for it.
- Proceeding with crypto without the Coinbase tax report — do not estimate disposals.
- Auto-applying 50% meals/entertainment rule — flag all, agent assesses.
- Re-asking setup questions already in the file header.

## First-Run Setup

Collect before any workflow:
1. FY (e.g. FY2025 = 1 Jul 2024 – 30 Jun 2025)
2. Tax agent name and firm
3. Income streams active this FY: PAYG / Sole Trader / Vanguard / Crypto / Other
4. Partner income: yes/no — if yes, collect taxable income amount (MLS assessment)
5. Private health insurance (hospital cover): yes/no

Store in file header. Do not re-ask.

## Workflows

**GATHER — stream by stream**

Step 1 — PAYG (TBST): Source: ATO income statement via MyGov. Collect: gross income, total tax withheld, reportable fringe benefits, reportable employer super. Collect D1–D10 deductions. Flag any claim >$300 without receipt.

Step 2 — Sole Trader (HatchFox): Check for bookkeeping export first. If exists → pull net income/loss. If not → flag: "Run /bookkeeping before final lodgment." Confirm: GST registered? BAS lodged?

Step 3 — Vanguard: Source: Vanguard Annual Tax Statement. Collect: unfranked/franked dividends, franking credits, capital gains distributions, units sold (date acquired, disposed, cost base, proceeds).

Step 4 — Crypto (Coinbase): Source: Coinbase tax report (Coinbase > Taxes > Reports). Do not proceed without the report. For each disposal: date acquired, disposed, cost base (AUD), proceeds (AUD). Flag: 50% CGT discount if held 12+ months. Staking rewards and airdrops = ordinary income.

Step 5 — Other Deductions: Personal super contributions + Notice of Intent lodged (Y/N), income protection insurance, charitable donations $2+ (DGR only), prior year tax agent fees, investment income expenses.

Step 6 — Partner Income + MLS Check: Collect partner's taxable income. If combined income >$186,001 and no hospital cover → flag MLS surcharge risk. See REFERENCE.md.

**REVIEW:** Flag missing source docs, deductions >$300 with no receipt, unresolved crypto disposals, HatchFox without bookkeeping export, missed super opportunity, MLS exposure.

**EXPORT:** Produces `Sasha - Person/Tax/Sasha - TaxReturn-[FY]-Staging.md`. Uses `_AI/TEMPLATES/tax-return-template.md`. Copy-paste ready for email to tax agent.

## Constraints

- AUS FY only. Never calculate final tax liability.
- Never guess — if data is missing, prompt for the source document.
- All amounts AUD. Sole trader figures GST-exclusive.
- Crypto: every disposal is a CGT event. Uncertain year = flag, do not estimate.
- Super: only claimable if contributed by 30 June AND Notice of Intent lodged before return filed.

## QA
Before closing this skill session:
- [ ] All income streams collected or flagged as missing source document
- [ ] No tax liability calculated — amounts only
- [ ] Crypto disposals complete or flagged pending report
- [ ] REVIEW run — all flags documented
- [ ] EXPORT file written to correct path
- [ ] Every response ended with NEXT MOVE

See REFERENCE.md for tax brackets, MLS thresholds, D-item categories, CGT rules, and lodgment deadlines.

Every response ends with NEXT MOVE.
