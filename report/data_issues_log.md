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
