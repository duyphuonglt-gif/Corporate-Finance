---
template: memo
title: "Stage 2 Company Selection — iQIYI Inc (IQ)"
to: "Adam Stauffer"
from: "Luong Duy Phuong"
date: "2026-05-14"
re: "Company Selection & Preliminary Financial Hypothesis Validation"
course: "BUS-629"
stage: 2
author: "Luong Duy Phuong"
company: "iQIYI Inc"
ticker: "IQ"
exchange: "NASDAQ"
---

# Stage 2 Company Selection — iQIYI Inc (IQ)

**To:** Adam Stauffer | **From:** Luong Duy Phuong | **Date:** 2026-05-14

---

## 1. Company Overview

iQIYI Inc (Ticker: IQ, NASDAQ ADR 1:7) is China's largest online entertainment platform (movies, dramas, anime), incorporated in the Cayman Islands. FY2025 (ended Dec 31, 2025): revenue –6.6% YoY, gross margin compressed 310 bps to 21.1%, net margin collapsed from +2.7% to –0.75%. Market cap $1.1B at $1.18/ADS (down 40.7% YTD).

---

## 2. Selection Rationale

iQIYI offers three analytically productive conditions: (1) a large EBITDA-to-net-margin gap (50.4% vs. –0.75%) that demands decomposition; (2) a VIE/ADR structure that layers tax, currency, and regulatory risk onto an already-strained income statement; and (3) audited U.S. GAAP financials via SEC Form 20-F, enabling direct ratio computation without reconciliation friction.

---

## 3. Data Availability & Sources

**Primary:** Form 20-F FY2025, SEC EDGAR, CIK 0001722608. Audited by Deloitte. Figures in RMB thousands; USD at RMB 6.9956 = $1.00.  
URL: https://www.sec.gov/Archives/edgar/data/1722608/000119312526107079/iq-20251231.htm

**Secondary:** Mergent by FTSE Russell (May 8, 2026) for beta and leverage ratios; iQIYI IR for FY2023 comparables.

**Known gaps:** Revenue mix (membership vs. advertising) not broken out in consolidated statements; subscriber count and ARPU not in audited statements.

---

## 4. Preliminary Observations

1. "I expect FY2026 gross margin to compress a further 100–200 bps because content costs were stable in absolute RMB (≈RMB 21.5B both years) while revenue fell 6.6%, indicating fixed-cost leverage works against iQIYI on a shrinking topline."

2. "I expect membership ARPU contraction — not subscriber count decline — to be the primary revenue driver because advertising revenue falls faster than membership in downturns, and gross margin compressed more than COGS-ratio expansion alone would predict."

3. "I expect the constant-currency revenue decline to be 4–5% rather than the reported 6.6% because USD appreciated 2–3% vs. CNY over FY2025."

4. "I expect the effective tax rate to revert toward 25% within two years because preferential HNTE rates (15%) for key subsidiaries expire in 2026–2028 and face regulatory renewal risk, increasing cost of capital by 150–200 bps."

5. "I expect beta (0.19 vs. S&P 500) to understate true equity risk because it cannot capture PRC-specific tail risks — content bans, VIE unwinding — which is why I add a 3% China risk premium to WACC."

---

## 5. Ratio Categories Preview

- **Profitability:** Gross / operating / net margin trend FY2023–25; ROA and ROC using after-tax operating income.
- **Leverage & Coverage:** Long-term Debt/Capital (43.1%), TIE (currently 0.25x — below safe threshold), Cash Coverage.
- **Efficiency:** Asset Turnover, Receivables Turnover, Inventory Turnover (licensed copyright inventory).
- **Valuation:** MVA, EVA (vs. 9% WACC), Market-to-Book, Du Pont ROE decomposition.

---

## 6. Data Collection Plan

**Step 1 — Balance Sheet (by May 14):** Extract `BAL_*` ranges for both years from the 20-F consolidated balance sheet; verify Assets = Liabilities + Equity before proceeding.  
**Step 2 — Income Statement & Cash Flow (by May 14):** Extract `INC_*` and `CASH_*`; reconcile interest expense from debt footnotes; confirm depreciation to verify EBITDA.  
**Step 3 — Assumptions & Ratio check (by May 14):** Enter share price $1.18, shares 962,959K ADS, WACC 9%, tax rate 25%. Confirm Ratios tab resolves with no `#REF!` or `#DIV/0!` errors; commit populated workbook to `models/builds/`.

---

**Word Count:** 530 | **Status:** Revised per Stage 2 feedback
