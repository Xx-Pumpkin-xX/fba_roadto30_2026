# Team B – axis 2 (supply side): method and findings (auto-generated from notebook/B1_axis2_supply_side.ipynb)

## Method

- **Sample:** DSE, SSI, VND, HCM, VCI, MBS (company data, financial statements Q4/2024–Q2/2026); SSI, VPS, TCBS for features; HNX for derivatives shares.
- **Lending = total loans (FS code 114)** for every broker, because DNSE does not split margin loans from sale-proceeds advances.
- **Measures:** 2.1 momentum (share change, P0 = Q4/2024–Q2/2025 vs P1 = Q4/2025–Q2/2026), 2.2 headroom (growth left before a binding limit), 2.3 efficiency (DSE rank among six brokers; derivatives among five with published fees), scored 1–5; 2.4 capability & price (net position vs peers), scored 0 / 2.5 / 5, where 5 requires at least one differentiator.
- **Checks:** 81 spreadsheet formulas re-computed and matched; quarterly figures reconcile to cumulative FS totals; 22 data fixes logged.

## Findings

- **Momentum:** derivatives share 14.8% → 25.0%; margin (share of sample loans) 5.2% → 5.0%; new accounts 24.9% → 18.4% (P1 = Q4/2025–Q1/2026).
- **Headroom:** margin ceiling used 58% (Q2/2026), about VND 4,577 bn left.
- **Efficiency:** lending spread 4.8 pp, rank 4/6; brokerage contribution margin on net revenue -40.8%, rank 6/6.
- **Capability & price:** new investors 2.5 (net +1, 0 differentiator); margin 2.5 (net +1, 0 differentiator); derivatives 2.5 (net +1, 0 differentiator).
- **Axis 2:** derivatives 3.375 > margin 3.125 > new investors 2.375. Leader(s) by scenario: Derivatives: 5; Margin: 2; Margin = Derivatives: 1.

## Limitations

- Derivatives efficiency ranks published fees, not realised revenue; 2.4 uses three peers for features and published prices.
- DNSE has not published Q2/2026 new accounts, so new-investor P1 covers Q4/2025–Q1/2026 only.
- DNSE is outside HOSE's top 10; the 10th broker's share (2.94%, Q2/2026) is used as an upper bound. The headroom score only changes if DNSE's true share exceeds 6.5%.
- Mixed 1–5 and 0/2.5/5 scales are averaged with equal weight, as agreed with the team leader.
- New-investor headroom compares DNSE's 2025 account share (13%) with an upper bound for its Q2/2026 HOSE brokerage share (2.94%): different periods and bases.
- The same derivatives fee enters twice in opposite directions: a higher fee ranks better on efficiency (2.3), a lower fee counts as a price advantage (2.4).
- Lending share uses Q4 and Q2 of each period only, and total loans (margin + advances) for every broker.
- Peers' exchange fees for SSI and VND are proxies; VCI does not disclose them, so its brokerage margin is on gross revenue.
