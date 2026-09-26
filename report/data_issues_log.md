# Team B – Data issues log and decisions

Source file: `data/raw/FBAR2_TEAM B DATASET.xlsx` (BA dataset, 25/09/2026). The raw file is **never edited by hand**; every correction is applied in `notebook/B1_axis2_supply_side.ipynb` and recorded in `data/clean/B_cleaning_log.csv`.

Status: ✅ resolved · ⚠️ resolved provisionally, needs confirmation · ❌ open

## 1. Issues that affect conclusions

### D01 – "Margin balance" mixes two definitions ✅
- **Where:** `2.1_Margin!C` and `2.3_Margin!C,F` vs `2.3_Margin!E,H`.
- **Problem:** implied advances (total loans − margin) are only rounding noise for DSE (0.2 / 0.1 bn), HCM (0.1 / 0) and VCI Q4/2025 (0.2 bn), but real for SSI, VND, MBS and VCI Q2/2026 (157–672 bn). So the "margin" figures for DSE, HCM and VCI Q4 are really **total loans (margin + sale-proceeds advances)**. The press confirms this for DSE: 5,832 (Q4/2025), 5,910 (Q1/2026) and 6,303 bn (Q2/2026) are "margin loans **and sale-proceeds advances**".
- **Decision:** all headroom, share and efficiency metrics use **total loans** (`2.3_Margin` E/H) for all six brokers. Ceiling utilisation on this basis is an **upper bound** [ASSUMPTION], because the 200% rule applies to margin loans; for DSE the difference is nil.
- **Impact:** DSE share of the sample becomes 4.92% (Q4/2025) and 5.14% (Q2/2026), instead of 4.99% and 5.19%.

### D02 – DSE funding cost had two versions ✅ (DSE) / ⚠️ (peers)
- **Where:** `2.2_Margin!V2` = 1.50% (interest expense only) vs `2.3_Margin!R2` = 6.29% (adds a hard-coded 240 bn in `O2`).
- **Resolution:** the BA located the source: DNSE 6M/2026 income statement **code 24** ("provisions … and borrowing cost of loans") = 248.926 bn, less the loan-impairment provision (note 29) of 8.964 bn = **239.96 bn** borrowing cost of loans. The notebook uses the exact figure. Funding cost A = 6.29% (main); B = 1.50% (sensitivity only).
- **Open:** code 24 is a standard line for every securities firm, but peers' code-24 borrowing cost is set to 0. Break-even (H1/2026) at which a peer would rank below DSE: **MBS 21 bn, SSI 192 bn, VND 202 bn** (HCM and VCI already rank below DSE).

### D03 – Ceiling utilisation: 58% or 72%? ✅
- The BA file correctly matches dates (Q2 lending / Q2 equity). The Masterplan's ~72% divides Q2/2026 lending by Q4/2025 equity.
- **Decision:** report both dates on the same definition: **Q4/2025 = 67.8%**, **Q2/2026 = 57.9%** (equity +26.5% after the 2026 capital raise). Headroom at Q2/2026 is about **VND 4,580 bn**, not 2,300–2,500 bn.

## 2. Date and format issues

| Code | Where | Problem | Fix | Status |
|---|---|---|---|---|
| D04 | `2.1_NewInvestors!H4` | Date parsed as 04/08/2026 (month-first), breaking chronological order | Corrected to **08/04/2026**; consistent with press (1.65 m accounts, 18/04/2026) | ✅ |
| D05 | `2.1_NewInvestors!B,H` | Dates stored as text dd/mm/yyyy | Parsed with `dayfirst=True` | ✅ |
| D06 | `2.1_NewInvestors!A5/E5` | Label FY/2025 but period column Q4/2025 | Treated as **full-year 2025** (matches the Masterplan); column E renamed `source_report` | ⚠️ |
| D07 | `2.1_NewInvestors!E2:E5, L3` | "Source" / "Source_ID" columns hold reporting periods | Split into period and source ID; `L3` source left blank | ⚠️ |
| D08 | `2.1_NewInvestors!J5` | 1.7 m accounts flagged ">" but growth computed as exact | Qualifier kept; growth reported as **at least +41.7%** | ✅ |
| D09 | `2.1_NewInvestors!A3` | "Late-2024" (19/12/2024) labelled Q4/2024 | Kept, noted as a mid-December figure | ✅ |
| D10 | `2.1_Margin!I11:I15` | One column mixes growth rates and pp | Units separated in the notebook | ✅ |
| D11 | `2.3_Derivatives!C6` | Text "300 or upon agreement" in a numeric column | Value 300, flag `negotiable=True` | ✅ |
| D12 | `2.3_Derivatives!F4` | Truncated label "[Asss" | Renamed `side_factor [ASSUMPTION]` | ✅ |

## 3. Duplicated figures checked

| Figure | Where | Result |
|---|---|---|
| DSE derivatives share H1/2026 = 25.47% | `2.3_Derivatives!G3` | ✅ DNSE's own H1 figure (press, 21/07/2026); the simple average of two quarters is 25.44% |
| DSE brokerage revenue H1/2026 = 222.1; lending income H1/2026 = 336.1 | `2.3_Brokerage!F2`, `2.3_Margin!J2` | ✅ match the press |
| DSE brokerage revenue H1/2025 = 122.8 | `2.3_Brokerage!B2` | ✅ back-derived from the press (119.5/2.44 + 102.5/1.388) |
| Total accounts, new-account share, derivatives contracts | 2.1 ↔ 2.2 sheets | ✅ consistent |
| DSE quarterly figures (Q1 + Q2 vs H1) | `data/clean/DNSE_quarterly_for_Leader.csv` | ✅ reconcile within 0.1 bn |

All **81** spreadsheet formulas were re-computed in Python and matched (`data/clean/B_validation_vs_excel.csv`).

## 4. Open items

| Code | Item | Owner |
|---|---|---|
| D02b | Split code 24 into provisions vs borrowing cost for SSI, VND, MBS | BA |
| D13 | VCI equity falls 4.8% in H1/2026 (`2.2_Margin!C6,E6`) – confirm (dividend or typo?) | BA |
| D14 | `2.1_Margin` has only 4 dates; Q4/2024 and Q2/2025 definitions unverified | BA |
| D15 | Only MBS is bank-affiliated in the sample; the bank-funding hypothesis cannot be tested well | Team |
| – | Source IDs S5–S7, M1.1–M4.2, D1–D7, A1, A2 are cited but undefined; several cells have no source | BA |
| – | Source of DSE stock-brokerage share 1.92% (`2.2_NewInvestors!B6`) – immaterial to the score below 6.5% | BA |

## 5. Corrections from official statements (26/09/2026)

Sources: financial statements of SSI, VND, HCM, VCI, MBS (Q4/2024 to Q2/2026) and DNSE annual reports 2024-2025, downloaded from official channels. Every figure with its page reference is in `data/sources/extracted_from_official_statements.csv`.

| Code | Change | Before | After | Source |
|---|---|---|---|---|
| R1 | Peers' income-statement code 24 checked | Unknown | SSI −41.4 bn (net reversal), VND provisions only, VCI 0, MBS 0.48 bn → no hidden borrowing cost; DSE stays #4 of 6 on net spread | Q2/2026 and Q4/2025 statements |
| D16 | HCM funding cost | — | No change: HCM books all borrowing cost in code 24 and the BA's 1,031.6 bn is that line | HCM Q4/2025 statement |
| D17 | DSE total loans Q4/2024 | 3,785 (margin only) | 3,881.59 (margin + advances) | DNSE Annual Report 2025, note 7(c) |
| D18 | SSI / VND / MBS Q4/2025 in `2.1_Margin` | 38,616 / 13,647 / 14,517 (margin only) | 38,940 / 14,319 / 15,041 (code 114) | Q4/2025 statements |
| D19 | DSE and VCI Q2/2025 | — | Kept; not yet verified (limitation) | — |
| D20 | New-account share | Press figures, mixed periods | FY2024 21.6%; 2025 by quarter 34/18/16/19%; FY2025 20.1% | DNSE Annual Reports 2024, 2025 |
| D21 | Derivatives share | 25.38% and 25.47% both used | 25.38% (Q2/2026) only | HNX quarterly |
| M1 | Margin and derivatives momentum | 12-month change on a mixed series | Average P0 (Q4/2024–Q2/2025) vs average P1 (Q4/2025–Q2/2026) on a consistent total-loans series | Masterplan 4.2 |
| M2 | Funding-cost scenario B (interest expense only) | In sensitivity check | Removed (peers' code 24 now verified) | — |

Result: axis-2 scores are unchanged (margin 3.33 = derivatives 3.33 > new investors 2.67, three of five measures), but every figure is now traceable to an official statement.

## 6. Open items (as of 26/09/2026)

- (Superseded by section 8.)
- Derivatives efficiency is a judgement score (2); the Masterplan rule for missing data would give 3 and change the leader. To be decided by the team.
- DSE stock-brokerage share 1.92% is not in DNSE's annual reports (only HNX 3.12% is disclosed).
- DSE and VCI Q2/2025 total loans not yet verified; market-wide new accounts Q4/2024 pending.

## 7. Capability and competition (26/09/2026, draft)

- **D21:** only one derivatives share is used: 25.38% (Q2/2026, HNX quarterly).
- **D22:** TCBS's 0.03% is the exchange fee, so TCBS's brokerage fee is counted as 0. Per the Leader, the %-based price indicators ignore exchange fees.
- **Table 3 (capability):** 88 cells (22 features × 4 brokers). 18 cells are provisional "Yes" awaiting confirmation; six of them are high-risk and are tested as "No" in scenario B.
- **Price data** comes from sheet `2.5_Pricing` of the BA dataset v2 (published fee schedules). Known issues:
  - Source IDs are reused across sheets.
  - The promo "reach" column is really a per-client cap.
- **Scores for 2.4 and 2.5 are drafts.** The rules are shown in section 5b of the notebook and await team approval.

## 8. Update 26/09/2026 (evening): quarterly statements, verified sources, new 2.4 rule

- **D19 (closed):** DSE Q2/2025 total loans = 5,052.62 bn (code 114, Q2/2025 statement); the dataset's 4,835 was margin-only. VCI Q2/2025 = 11,306.62 bn, matches the dataset.
- **D23:** codes 06, 03 and 114 read for 6 brokers × Q3/2025–Q2/2026 from 24 statements (`data/sources/bctc_quarterly_4Q_official.csv`). Quarterly flows reconcile to the cumulative 6M/9M/FY columns; Q1+Q2/2026 match the dataset's H1 figures within 0.6 bn. Four OCR misreads were caught and visually corrected (VND Q1/2026 code 114, MBS Q1/2026 codes 114 and 06, VCI Q2/2026 code 03).
- **D24:** derivatives shares Q3/2025–Q2/2026 match HNX releases (Q1/2026 via Vietstock citing HNX). 25.47% is the H1/2026 figure, not a quarter.
- **HNX counts futures volume one-way**, so the ×2 side factor in the revenue estimate is correct.
- **VSDC new accounts:** Q4/2024 more than 432,000 (DNSE share ≈ 23.0%, an upper bound); Q2/2026 more than 768,000 (DNSE figure not published, left blank).
- **Derivatives fees split into broker / exchange (2,700) / VSDC (2,550) per contract per side**, with links (`2.4_derivatives_fee_3cols.csv`). SSI's broker fee is taken from the dataset (link supplied, not re-read).
- **Table 3:** all 18 provisional cells checked by a teammate. Nobody offers a stock demo account; VPS has no client API and copy-trade only in its derivatives app; VPS real-time margin-call alerts remain "Unknown".
- **2.4 rule changed (Leader):** 2.5 merged into 2.4, scored 0 / 2.5 / 5 against the market (features + price vs peer median). All three groups score 5 (net +1, price below median, no parity gaps).
- **Result:** margin 3.75 = derivatives 3.75 > new investors 3.25; derivatives efficiency = 3 would make derivatives lead (4.00).
- **Possible origin of "1.92%":** Mirae Asset's H1/2026 derivatives share in the same HNX table; not a DNSE figure.
- **Follow-up (26/09, late):** SSI derivatives fee re-checked on SSI's site (self-directed online 2,000 / 1,500 / 500 per contract; matches the dataset). Derivatives shares Q4/2024–Q2/2025 now linked to HNX (7/7 quarters verified). DNSE new accounts Q1/2026 = 142,000, 18% share (Vietstock 18/04/2026); used only in the sensitivity check (new-investor P0/P1 partial: −6.5 pp, score 1 → axis 3.00, leader unchanged). VPS real-time margin alerts set to "Yes" on weak evidence (no score effect).

## 9. Leader review (26/09/2026, night)

- **D25:** DSE equity at 30/6/2026 = 5,440.25 bn (reviewed half-year statement) → margin headroom 4,577 bn.
- **D26:** the 1.92% "stock-brokerage share" was Mirae Asset's derivatives share. DNSE is outside HOSE's top 10, so the 10th broker's share (2.94%, Q2/2026) is used as an upper bound; the headroom score is unchanged unless DNSE's true share exceeds 6.5%.
- **Derivatives efficiency** is now rule-based: DNSE's low-volume fee ranks #3 of 5 → 3 (old judgement 2 kept as a sensitivity).
- **New-investor momentum** now uses P0/P1 like the other groups (P1 = Q4/2025–Q1/2026; Q2/2026 not published): −6.5 pp → 1.
- D01 wording corrected (DNSE advances: 584 bn end-2025, 178 bn at 30/6/2026); D02b closed; Figure 10 title restated as a figure.
- **Table 4:** derivatives 4.00 > margin 3.75 > new investors 3.00. Leaders across Table PL2: derivatives 5/8, margin 2/8, tie 1/8 (see `Table_PL2_axis2_sensitivity.csv`).
- **Net brokerage revenue (Table PL1):** fees paid to the exchanges/VSDC in H1/2026 read from the reviewed half-year statements: DSE 113.4, HCM 185.4, MBS 104.4 bn (explicit lines); SSI 264.4 and VND 181.2 bn (by-nature 'brokerage charges', a proxy); VCI not disclosed (limitation). See `data/sources/brokerage_fees_H1_2026.csv` and `PL1_inputs_for_Leader.csv`.
- **The ~94 bn gap:** DNSE's statements do not split brokerage revenue by product. Net brokerage revenue 108.7 bn less an estimated 14.6 bn of derivatives fees leaves 94.1 bn, close to the 90.5 bn of partner commissions paid (note 30). This suggests the non-derivatives revenue comes mainly from partner-channel accounts and is largely passed on to partners; it cannot be confirmed from the statements alone.
- **Checklist files added:** `2.1_market_share_quarterly.csv`, `2.5_competition.csv` (price position, derivatives concentration, bank-affiliated peers), `PL1_inputs_for_Leader.csv`.
