# Bookkeeping — Reference

## Australian Financial Year

| FY | Start | End | Tax return due |
|---|---|---|---|
| FY2025 | 1 Jul 2024 | 30 Jun 2025 | 31 Oct 2025 (or 15 May 2026 via tax agent) |
| FY2026 | 1 Jul 2025 | 30 Jun 2026 | 31 Oct 2026 (or 15 May 2027 via tax agent) |

**BAS (annual lodgement):**
Due 31 October after FY end (or 28 February if not required to lodge by October).
Covers: GST collected, GST paid (claimable), net GST position.

---

## Entity Context

**[Business]** — sole trader business. All business income and expenses.
**[Employer]** — employment income (PAYG) and work-related D-item deductions.
**Personal** — not deductible. Excluded from ledger (noted only if splitting mixed-use items).

---

## Income Categories

| Code | Category | Entity | Notes |
|---|---|---|---|
| INC-SVC | Services / Consulting | [Business] | Primary trading income |
| INC-PROD | Product / Digital Sales | [Business] | Games, assets, digital goods |
| INC-ROY | Royalties / Licensing | [Business] | IP, music, content licensing |
| INC-GRANT | Grants / Subsidies | [Business] | Government or private grants |
| INC-INT | Interest | [Business] | Business account interest |
| INC-REF | Refunds Received | [Business] | Reduces prior expenses |
| INC-OTHER | Other Income | [Business] | Flag and describe |

---

## Expense Categories

| Code | Category | Entity | Notes |
|---|---|---|---|
| EXP-SW | Software & Subscriptions | [Business] or [Employer] | Assign to whichever entity uses it |
| EXP-EQUIP | Equipment & Hardware | [Business] or [Employer] | Flag assets >$20k for write-off |
| EXP-OFFICE | Office Supplies | [Business] or [Employer] | Stationery, consumables |
| EXP-MKTG | Marketing & Advertising | [Business] | Ads, design, promotion |
| EXP-TRAVEL | Travel | [Business] or [Employer] | Flag purpose and entity |
| EXP-VEH | Vehicle | [Business] or [Employer] | Record method at setup (km or logbook) |
| EXP-PHONE | Phone & Internet | [Business] / [Employer] | Split % by entity if mixed |
| EXP-HOME | Home Office | [Business] / [Employer] | Split % by entity if mixed |
| EXP-PROF | Professional Services | [Business] | Accountant, legal, consultants |
| EXP-TRAIN | Training & Development | [Business] or [Employer] | Courses, books, conferences |
| EXP-MEALS | Meals & Entertainment | [Business] or [Employer] | FLAG ALL — accountant assesses |
| EXP-INSURE | Insurance | [Business] | Business insurance, PI, PL |
| EXP-BANK | Bank Fees | [Business] | Account fees, merchant fees |
| EXP-RENT | Rent & Utilities | [Business] | Studio/office rent, power |
| EXP-ASSET | Asset Purchase | [Business] or [Employer] | Flag all — write-off rules apply |
| EXP-SUB | Subcontractors | [Business] | Flag if no ABN provided |
| EXP-OTHER | Other | [Business] or [Employer] | Flag and describe |

---

## Ledger Entry Format

| Date | Type | Entity | Vendor/Source | Description | Amount (inc GST) | GST | Amount (ex GST) | GST-Free | Category | Receipt | Notes |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 15 Aug 2024 | Expense | [Business] | Adobe | Creative Cloud annual | $879.89 | $79.99 | $799.90 | No | EXP-SW | Yes | Overseas — flag reverse-charge |
| 10 Sep 2024 | Expense | [Employer] D-item | Officeworks | Notebook + pens | $44.00 | $4.00 | $40.00 | No | EXP-OFFICE | Yes | Work use only |

**Receipt field values:** Yes / No / Estimated
**Entity field values:** [Business] / [Employer] D-item / Split (with % noted)

---

## Receipt Record Format (single transaction)

```
RECEIPT RECORD
──────────────────────────────────────────
Date:              DD MMM YYYY
Type:              Income / Expense
Entity:            [Business] / [Employer] D-item / Split [X% [Business] / Y% [Employer]]
Vendor/Source:     [Name]
Description:       [What was purchased or received]
Amount (inc GST):  $X,XXX.XX
GST Component:     $XXX.XX
Amount (ex GST):   $X,XXX.XX
GST-Free:          Yes / No
Category:          [CODE — Category Name]
Receipt:           Yes / No / Estimated
Reference:         [Invoice #, receipt #, or N/A]
Notes:             [Flags, business purpose, split rationale]
──────────────────────────────────────────
```

---

## GST Rules

- **Taxable supply:** 10% GST. Calculate: amount ÷ 11 = GST component.
- **GST-free:** Basic food, health, education, exports. Mark explicitly.
- **Input-taxed:** Financial services, residential rent. No GST credit claimable.
- **Reverse-charge (overseas digital services):** Adobe, Google Ads, Canva, Spotify, GitHub etc.
  GST may apply but vendor doesn't charge it. Flag every overseas subscription for accountant.

When in doubt — log the amount, flag it, let accountant decide.

---

## Asset Write-Off Rules (AUS FY2025/FY2026)

- **Instant asset write-off threshold:** $20,000 per asset (until 30 June 2026)
- **After 30 June 2026:** Reverts to $1,000 unless extended. Flag all assets logged in FY2026.
- **Eligibility:** Business turnover under $10 million. Asset first used for business purpose in the FY.
- **Per-asset basis:** Multiple assets can each be written off individually.
- **Action:** Flag every asset purchase in EXP-ASSET. Include purchase price and intended use.
  Accountant determines write-off eligibility.

---

## Accountant Export Format

```
FINANCIAL SUMMARY — [FY or Date Range]
Prepared for: [Person]
ABN: XX XXX XXX XXX
Trading as: [Business name]
Entity type: Sole Trader
Prepared: [Date generated]
For accountant use — not a tax return

════════════════════════════════════════
SECTION 1 — [BUSINESS] (SOLE TRADER)
════════════════════════════════════════

INCOME
  [Category]                    $X,XXX.XX
  Total Income (inc. GST):      $XX,XXX.XX
  GST Collected:                $X,XXX.XX
  Total Income (ex. GST):       $XX,XXX.XX

EXPENSES — by category (sorted by amount)
  [Category]                    $X,XXX.XX
  ...
  Total Expenses (inc. GST):    $XX,XXX.XX
  GST Paid (claimable):         $X,XXX.XX
  Total Expenses (ex. GST):     $XX,XXX.XX

GST POSITION
  GST Collected:                $X,XXX.XX
  GST Paid (claimable):         $X,XXX.XX
  Net GST payable/refundable:   $X,XXX.XX

NET PROFIT/LOSS (ex. GST):      $XX,XXX.XX

════════════════════════════════════════
SECTION 2 — [EMPLOYER] EMPLOYMENT D-ITEM DEDUCTIONS
════════════════════════════════════════

  [Category]                    $X,XXX.XX
  ...
  Total D-item claims (ex. GST): $X,XXX.XX
  Note: These are work-related deductions against employment income.
  Confirm eligibility with accountant.

════════════════════════════════════════
SECTION 3 — OTHER INCOME (REFERENCE ONLY)
════════════════════════════════════════

  PAYG Employment Income ([Employer]): $XX,XXX.XX  ← from MyGov income statement
  Partner Income:                      $XX,XXX.XX  ← from partner's MyGov income statement
  Note: These are not tracked in this ledger. Provided for accountant reference only.

════════════════════════════════════════
SECTION 4 — FLAGGED ITEMS FOR ACCOUNTANT REVIEW
════════════════════════════════════════

  • Meals/entertainment entries — deductibility to be assessed
  • Overseas subscriptions — reverse-charge GST to be assessed
  • Mixed-use items — split % provided, confirm apportionment
  • Assets flagged for instant write-off assessment
  • Items with no receipt — marked Estimated
  • [Any other flags]
```

---

## Parsing Raw Input — Rules

1. **Date** — required. If missing, ask.
2. **Amount** — required. Assume inc. GST unless stated. Calculate GST as amount ÷ 11.
3. **Entity** — required. [Business] or [Employer] D-item? If unclear, present both and ask.
4. **Vendor/Source** — required. Use merchant name from bank lines.
5. **Category** — infer from description. If ambiguous, present 2 options and ask.
6. **GST-free** — flag if vendor/item is likely GST-free. Confirm before logging.
7. **Receipt** — ask if not stated. Default to No if user doesn't confirm.
8. **Reference** — optional. Log N/A if not provided.

Present as confirmation table before writing. User confirms or corrects. Then write.

---

## Mixed-Use Split Prompt

When an expense is used for both [Business] and [Employer] work (or personal):

```
Mixed-use item detected: [description]
What % was for each purpose?
  [Business] (sole trader business): ___%
  [Employer] (employment/work):      ___%
  Personal (non-deductible):         ___%
  Total must = 100%
```

Log as a split entry. Show [Business] and [Employer] amounts as separate line items in the export.

---

## Home Office Setup (collected at first run)

```
Home office deduction method?
A) Fixed rate — 70c per hour worked from home (covers energy, phone, internet, stationery)
   Requires: record of hours worked from home throughout the year
   Additional claims allowed: decline in value of assets, repairs, cleaning
B) Actual cost — % of real home running costs based on workspace m²
   Requires: floor plan measurements, all utility bills

Which method? If A: track hours weekly — do not rely on estimates at year end.
```

---

## Vehicle Setup (collected at first run)

```
Vehicle deduction method?
A) Cents per km — 88c/km, max 5,000km, no logbook required
   Covers all costs (fuel, rego, insurance, depreciation)
   Maximum deduction: $4,400/year
B) Logbook method — % of actual vehicle costs, no km cap
   Requires: 12-week continuous logbook, odometer records

Which method? If B: logbook business use %? Total vehicle costs this FY?
```

---

## Record Retention

ATO requirement: **5 years** from date of transaction or when records received.
Digital copies (photos of receipts) acceptable. Must be clear and legible.
