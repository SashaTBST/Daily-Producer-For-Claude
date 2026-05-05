# /bookkeeping — Reference

## AUS Financial Year
| FY | Start | End | Tax return due |
|---|---|---|---|
| FY2025 | 1 Jul 2024 | 30 Jun 2025 | 31 Oct 2025 (or 15 May 2026 via tax agent) |
| FY2026 | 1 Jul 2025 | 30 Jun 2026 | 31 Oct 2026 (or 15 May 2027 via tax agent) |

BAS (annual lodgement): due 31 October after FY end.

## Entity Context
- **HatchFox** — Sasha's sole trader business. All business income and expenses.
- **TBST** — Sasha's employer. Employment income (PAYG) and work-related D-item deductions.
- **Personal** — Not deductible. Excluded from ledger (noted only if splitting mixed-use items).

## Income Categories
| Code | Category | Entity |
|---|---|---|
| INC-SVC | Services / Consulting | HatchFox |
| INC-PROD | Product / Digital Sales | HatchFox |
| INC-ROY | Royalties / Licensing | HatchFox |
| INC-GRANT | Grants / Subsidies | HatchFox |
| INC-INT | Interest | HatchFox |
| INC-REF | Refunds Received | HatchFox |
| INC-OTHER | Other Income | HatchFox |

## Expense Categories
| Code | Category | Notes |
|---|---|---|
| EXP-SW | Software & Subscriptions | Assign to entity that uses it |
| EXP-EQUIP | Equipment & Hardware | Flag assets >$20k for write-off |
| EXP-OFFICE | Office Supplies | Stationery, consumables |
| EXP-MKTG | Marketing & Advertising | Ads, design, promotion |
| EXP-TRAVEL | Travel | Flag purpose and entity |
| EXP-VEH | Vehicle | Record method at setup |
| EXP-PHONE | Phone & Internet | Split % by entity if mixed |
| EXP-HOME | Home Office | Split % by entity if mixed |
| EXP-PROF | Professional Services | Accountant, legal, consultants |
| EXP-TRAIN | Training & Development | Courses, books, conferences |
| EXP-MEALS | Meals & Entertainment | FLAG ALL — accountant assesses |
| EXP-INSURE | Insurance | Business insurance, PI, PL |
| EXP-BANK | Bank Fees | Account fees, merchant fees |
| EXP-ASSET | Asset Purchase | Flag all — write-off rules apply |
| EXP-SUB | Subcontractors | Flag if no ABN provided |
| EXP-OTHER | Other | Flag and describe |

## Ledger Entry Format
| Date | Type | Entity | Vendor/Source | Description | Amount (inc GST) | GST | Amount (ex GST) | GST-Free | Category | Receipt | Notes |
|---|---|---|---|---|---|---|---|---|---|---|---|

Receipt values: Yes / No / Estimated. Entity values: HatchFox / TBST D-item / Split (with %)

## Receipt Record Format
```
RECEIPT RECORD
──────────────
Date:              DD MMM YYYY
Type:              Income / Expense
Entity:            HatchFox / TBST D-item / Split [X% HatchFox / Y% TBST]
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
```

## GST Rules
- Taxable supply: 10% GST. Calculate: amount ÷ 11 = GST component.
- GST-free: basic food, health, education, exports. Mark explicitly.
- Input-taxed: financial services, residential rent. No GST credit claimable.
- Reverse-charge (overseas digital): Adobe, Google Ads, Canva, Spotify, GitHub etc. Flag every overseas subscription.

## Asset Write-Off (FY2025/FY2026)
- Instant asset write-off threshold: $20,000 per asset (until 30 June 2026)
- After 30 June 2026: reverts to $1,000 unless extended
- Eligibility: business turnover under $10 million. Asset first used for business in the FY.
- Flag every asset in EXP-ASSET. Accountant determines write-off eligibility.

## Home Office (collected at first run)
- A) Fixed rate — 70c per hour worked from home. Requires actual record of hours.
- B) Actual cost — % of real home running costs based on workspace m². Requires floor plan + utility bills.

## Vehicle (collected at first run)
- A) Cents per km — 88c/km, max 5,000km, no logbook required. Max deduction: $4,400/year.
- B) Logbook method — % of actual vehicle costs, no km cap. Requires 12-week continuous logbook.

## Parsing Raw Input Rules
1. Date — required. If missing, ask.
2. Amount — required. Assume inc. GST unless stated. Calculate GST as amount ÷ 11.
3. Entity — required. HatchFox or TBST D-item? If unclear, present both options and ask.
4. Category — infer from description. If ambiguous, present 2 options and ask.
5. Receipt — ask if not stated. Default to No if user doesn't confirm.

Present as confirmation table before writing. User confirms or corrects. Then write.

## Record Retention
ATO requirement: 5 years from date of transaction. Digital copies (photos) acceptable.
