---
template: spec
purpose: "Technical specification for model-driven ratio analysis — defines scope, inputs, formulas, validation, and analysis requirements precisely enough that any competent executor (human or LLM) can produce correct output"
audience: MBA academic
fields_required: [title, author, date, version, company, scope, model_architecture, data_inputs, named_range_conventions, derived_inputs, ratio_definitions, validation, analysis_requirements, dupont, recommendations, output_format, references]
naming_convention: "YYYY-MM-DD-{slug}.md"
course: BUS-629
stage: 4
notes: "Authored for BUS-629 Stage 4 — iQIYI Inc. (IQ, NASDAQ) FY2025 ratio analysis. Spec v2.0 incorporates 5 rounds of HIL iteration and cross-vendor testing (Claude + Gemini); all 10 identified gaps closed. D&A treatment note: INC_depreciation = 0 (iQIYI embeds D&A in COGS); Cash Coverage uses CASH_depreciation_amortization from CF Statement. Market-to-Book and MVA use mixed-unit '000 inputs per Stage 1 template convention — no FX conversion applied."
---

# iQIYI Inc — Accounting & Performance Ratios: Technical Specification

**Author:** Luong Duy Phuong
**Date:** 2026-05-16
**Version:** 2.0
**Company:** iQIYI Inc (Ticker: IQ, NASDAQ)

---

## 1. Scope & Objective

**Company:** iQIYI Inc (IQ) — China's largest online entertainment platform (subscription streaming, advertising, content distribution). VIE structure; incorporated Cayman Islands; trades on NASDAQ as ADR (1 ADS = 7 Class A ordinary shares).

**Fiscal Period:** FY2025 (ended December 31, 2025) as current year; FY2024 as prior year (start-of-year base for turnover and return ratios).

**Reporting Standard:** U.S. GAAP (SEC Form 20-F, audited by Deloitte Touche Tohmatsu).

**Reporting Currency:** RMB thousands (RMB '000), unless marked USD. USD translation at RMB 6.9931 = US$1.00 (Federal Reserve H.10, December 31, 2025). Market assumptions (share price, market cap) stated in USD thousands.

**Analytical Objective:** Decompose iQIYI's FY2025 performance deterioration — net margin collapse from +2.7% (FY2024) to –0.75% (FY2025) — across profitability, efficiency, leverage, and liquidity dimensions. Identify which ratio categories are the primary drivers of value destruction and generate management-actionable recommendations.

**Intended Audience:** MBA finance academic audience. Analysis must explain methodology, not merely report outputs. Ratio interpretations should connect to underlying business economics. Strategic recommendations must be evidence-based and operationally specific.

---

## Part A — Model Specification

### 2. Model Architecture

**Tab layout (6 tabs):**

| Tab | Purpose | Input/Output |
|-----|---------|--------------|
| Cover | Instructions, color key, named-range legend | Reference only |
| Balance Sheet | FY2025 (current) + FY2024 (prior) balance sheet data | Input |
| Income Statement | FY2025 income statement with % of sales column | Input |
| Cash Flow Statement | FY2025 cash flow by activity | Input |
| Ratios | Analyst assumptions, derived inputs, all ratio outputs | Calculation + Output |
| Notes | Company metadata, sources, AI usage, self-checks | Reference |

**Color coding convention:**
- Yellow background = DATA INPUTS (overwrite with company figures)
- Light-blue background + blue text = ANALYST ASSUMPTIONS (share price, WACC, tax rate, shares outstanding)
- Green text = FORMULAS (cross-sheet references; do not overwrite)
- Gray background = RATIO OUTPUTS (computed; do not overwrite)

**Data flow:** Balance Sheet / Income Statement / Cash Flow tabs → named ranges → Ratios tab derived inputs → ratio outputs. All cross-tab references use named ranges, not cell addresses.

---

### 3. Data Inputs

All values in RMB thousands unless column specifies USD. Source: iQIYI Form 20-F FY2025, SEC EDGAR CIK 0001722608.

#### Balance Sheet — Current Year (FY2025)

| Named Range | Description | Value (RMB '000) |
|-------------|-------------|-----------------|
| `BAL_cash_marketable_securities_curr` | Cash and marketable securities | 4,354,275 |
| `BAL_receivables_curr` | Receivables | 2,522,668 |
| `BAL_inventories_curr` | Inventories (licensed copyrights) | 447,507 |
| `BAL_other_current_assets_curr` | Other current assets | 2,965,845 |
| `BAL_assets_current_curr` | Total current assets | 10,290,295 |
| `BAL_ppe_gross_curr` | Property, plant & equipment (net, reported combined) | 903,427 |
| `BAL_accumulated_depreciation_curr` | Accumulated depreciation | 0 *(see note A)* |
| `BAL_fixed_assets_net_curr` | Net tangible fixed assets | 903,427 |
| `BAL_intangibles_curr` | Intangible assets (goodwill) | 3,820,823 |
| `BAL_other_assets_curr` | Other assets | 31,667,190 |
| `BAL_assets_total_curr` | Total assets | 46,681,735 |
| `BAL_debt_short_term_curr` | Debt due for repayment (short-term) | 4,690,642 |
| `BAL_accounts_payable_curr` | Accounts payable | 6,652,432 |
| `BAL_other_current_liabilities_curr` | Other current liabilities | 10,724,233 |
| `BAL_liabilities_current_curr` | Total current liabilities | 22,067,307 |
| `BAL_debt_long_term_curr` | Long-term debt | 10,080,824 |
| `BAL_other_long_term_liabilities_curr` | Other long-term liabilities | 1,224,678 |
| `BAL_liabilities_total_curr` | Total liabilities | 33,372,809 |
| `BAL_common_stock_curr` | Common stock and paid-in capital | 56,026,664 |
| `BAL_retained_earnings_curr` | Retained earnings | –42,717,738 |
| `BAL_equity_shareholders_curr` | Total shareholders' equity | 13,308,926 |

> **Note A — PP&E gross/net:** iQIYI reports PP&E net of accumulated depreciation in primary statements. Gross cost and accumulated depreciation are not separately disclosed in the consolidated balance sheet. `BAL_accumulated_depreciation_curr` = 0 is a model placeholder; `BAL_fixed_assets_net_curr` carries the net figure. D&A for cash coverage is sourced from the Cash Flow Statement (see `CASH_depreciation_amortization`).

#### Balance Sheet — Prior Year (FY2024)

| Named Range | Description | Value (RMB '000) |
|-------------|-------------|-----------------|
| `BAL_cash_marketable_securities_prior` | Cash and marketable securities | 3,529,679 |
| `BAL_receivables_prior` | Receivables | 2,191,178 |
| `BAL_inventories_prior` | Inventories (licensed copyrights) | 388,718 |
| `BAL_other_current_assets_prior` | Other current assets | 3,417,661 |
| `BAL_assets_current_prior` | Total current assets | 9,527,236 |
| `BAL_ppe_gross_prior` | Net tangible fixed assets (net reported) | 877,982 |
| `BAL_accumulated_depreciation_prior` | Accumulated depreciation | 0 *(see note A)* |
| `BAL_fixed_assets_net_prior` | Net tangible fixed assets | 877,982 |
| `BAL_intangibles_prior` | Intangible assets (goodwill) | 3,820,823 |
| `BAL_other_assets_prior` | Other assets | 31,534,484 |
| `BAL_assets_total_prior` | Total assets | 45,760,525 |
| `BAL_debt_short_term_prior` | Debt due for repayment (short-term) | 4,197,348 |
| `BAL_accounts_payable_prior` | Accounts payable | 6,482,209 |
| `BAL_other_current_liabilities_prior` | Other current liabilities | 10,797,776 |
| `BAL_liabilities_current_prior` | Total current liabilities | 21,477,333 |
| `BAL_debt_long_term_prior` | Long-term debt | 9,387,405 |
| `BAL_other_long_term_liabilities_prior` | Other long-term liabilities | 1,522,023 |
| `BAL_liabilities_total_prior` | Total liabilities | 32,386,761 |
| `BAL_common_stock_prior` | Common stock and paid-in capital | 55,624,272 |
| `BAL_retained_earnings_prior` | Retained earnings | –42,250,508 |
| `BAL_equity_shareholders_prior` | Total shareholders' equity | 13,373,764 |

#### Income Statement (FY2025)

| Named Range | Description | Value (RMB '000) |
|-------------|-------------|-----------------|
| `INC_sales` | Net sales (total revenues) | 27,291,300 |
| `INC_cost_goods_sold` | Cost of goods sold (cost of revenues) | 21,542,347 |
| `INC_sga` | Selling, general & administrative expenses | 5,519,638 |
| `INC_depreciation` | Depreciation on IS | 0 *(see note B)* |
| `INC_ebit` | Earnings before interest and taxes (EBIT) | 229,315 |
| `INC_other_income` | Other income | 620,800 |
| `INC_interest_expense` | Interest expense | 909,616 |
| `INC_taxable_income` | Taxable income | –59,501 |
| `INC_taxes` | Taxes | 144,542 |
| `INC_net` | Net income | –204,043 |
| `INC_dividends` | Dividends | 0 |
| `INC_addition_retained_earnings` | Addition to retained earnings | –204,043 |

> **Note B — Depreciation on IS:** iQIYI embeds all D&A within `INC_cost_goods_sold` and does not report a separate depreciation line on the income statement. `INC_depreciation` = 0 is correct for this model. For Cash Coverage Ratio, D&A is sourced from `CASH_depreciation_amortization` (Cash Flow Statement add-back).

#### Cash Flow Statement (FY2025)

| Named Range | Description | Value (RMB '000) |
|-------------|-------------|-----------------|
| `CASH_operating` | Cash provided by operations | 105,801 |
| `CASH_depreciation_amortization` | D&A add-back (operating section) | 13,264,120 |
| `CASH_investments` | Cash used for investments | –327,435 |

#### Analyst Assumptions

| Named Range | Description | Value | Unit |
|-------------|-------------|-------|------|
| `yearCurrent` | Current fiscal year | 2025 | — |
| `yearStart` | Prior fiscal year | 2024 | — |
| `share_price` | ADS closing price (Dec 31, 2025) | 1.18 | USD |
| `shares_outstanding` | Shares outstanding (diluted, ADS basis) | 962,959 | '000 ADS |
| `cost_capital` | WACC (analyst assumption) | 0.09 | decimal |
| `tax_rate` | Statutory tax rate (PRC) | 0.25 | decimal |

---

### 4. Named Range Conventions

| Prefix | Meaning | Year suffix |
|--------|---------|-------------|
| `BAL_` | Balance sheet line item | `_curr` = FY2025; `_prior` = FY2024 |
| `INC_` | Income statement item | No suffix (current year only) |
| `CASH_` | Cash flow item | No suffix (current year only) |
| `RATIO_` | Key ratio reused in Du Pont decomposition | No suffix |
| `startYear_` | Alias for prior-year balance used as period-start denominator | = `BAL_*_prior` |
| `currentYear_` | Alias for current-year balance or derived current-year figure | = `BAL_*_curr` or formula |
| `avg_` | Average of start-of-year and current-year | = AVERAGE(startYear_*, currentYear_*) |

Four ratios are named for Du Pont reuse:
- `RATIO_asset_turnover` = Asset Turnover
- `RATIO_operating_profit_margin` = Operating Profit Margin
- `RATIO_leverage` = Leverage Ratio (Assets/Equity)
- `RATIO_debt_burden` = Debt Burden (Net Income / After-Tax Operating Income)

> **Model-specific named range — `CASH_depreciation_amortization`:** This named range is **not** present in the Stage 1 ratios template. It must be defined manually in the model, pointing to the D&A add-back line in the operating section of the Cash Flow Statement tab. Value: **13,264,120 RMB '000**. This is the only valid source of D&A for the Cash Coverage ratio — do not substitute `INC_depreciation` (which equals 0 in this model; see Note B, §3).

---

### 5. Derived Inputs

All formulas expressed in named-range notation. Executor must verify computed values match those below before running ratio analysis.

| Named Range | Formula | Computed Value | Unit |
|-------------|---------|----------------|------|
| `market_capitalization` | `share_price × shares_outstanding` | 1,136,292 | USD '000 |
| `startYear_equity` | `BAL_equity_shareholders_prior` | 13,373,764 | RMB '000 |
| `startYear_inventory` | `BAL_inventories_prior` | 388,718 | RMB '000 |
| `startYear_receivables` | `BAL_receivables_prior` | 2,191,178 | RMB '000 |
| `startYear_total_assets` | `BAL_assets_total_prior` | 45,760,525 | RMB '000 |
| `startYear_total_capitalization` | `BAL_debt_long_term_prior + BAL_equity_shareholders_prior` | 22,761,169 | RMB '000 |
| `currentYear_after_tax_operating_income` | `INC_net + (1 − tax_rate) × INC_interest_expense` | 478,169 | RMB '000 |
| `currentYear_daily_sales_average` | `INC_sales / 365` | 74,770.7 | RMB '000 |
| `currentYear_equity` | `BAL_equity_shareholders_curr` | 13,308,926 | RMB '000 |
| `currentYear_cash_marketable_securities` | `BAL_cash_marketable_securities_curr` | 4,354,275 | RMB '000 |
| `currentYear_assets_current` | `BAL_assets_current_curr` | 10,290,295 | RMB '000 |
| `currentYear_liabilities_current` | `BAL_liabilities_current_curr` | 22,067,307 | RMB '000 |
| `currentYear_cost_goods_sold_daily` | `INC_cost_goods_sold / 365` | 59,020.1 | RMB '000 |
| `currentYear_debt_long_term` | `BAL_debt_long_term_curr` | 10,080,824 | RMB '000 |
| `currentYear_working_capital_net` | `BAL_assets_current_curr − BAL_liabilities_current_curr` | –11,777,012 | RMB '000 |
| `currentYear_assets_total` | `BAL_assets_total_curr` | 46,681,735 | RMB '000 |
| `currentYear_total_capitalization` | `currentYear_debt_long_term + currentYear_equity` | 23,389,750 | RMB '000 |
| `currentYear_liabilities_total` | `BAL_liabilities_total_curr` | 33,372,809 | RMB '000 |
| `avg_equity` | `AVERAGE(startYear_equity, currentYear_equity)` | 13,341,345 | RMB '000 |
| `avg_total_assets` | `AVERAGE(startYear_total_assets, currentYear_assets_total)` | 46,221,130 | RMB '000 |
| `avg_total_capitalization` | `AVERAGE(startYear_total_capitalization, currentYear_total_capitalization)` | 23,075,460 | RMB '000 |

---

### 6. Ratio Definitions & Formulas

All ratios computed on Ratios tab. Values from Stage 3 populated workbook (FY2025).

#### Performance

| Ratio | Formula (named-range notation) | Value | Unit |
|-------|-------------------------------|-------|------|
| Market Value Added (MVA) | `market_capitalization − currentYear_equity` | –12,172,634 | '000 |
| Market-to-Book | `market_capitalization / currentYear_equity` | 0.085x | x |
| Economic Value Added (EVA) | `currentYear_after_tax_operating_income − (cost_capital × startYear_total_capitalization)` | –1,570,336 | RMB '000 |

> **Unit note for Performance ratios:** `market_capitalization` is in USD '000; `currentYear_equity` is in RMB '000. Both are used directly in the formula per the Stage 1 template convention — no FX conversion is applied. MVA and Market-to-Book are computed on a mixed-unit basis consistent with the template.

#### Profitability

| Ratio | Formula | Value | Unit |
|-------|---------|-------|------|
| ROA | `currentYear_after_tax_operating_income / startYear_total_assets` | 1.04% | % |
| ROC | `currentYear_after_tax_operating_income / startYear_total_capitalization` | 2.10% | % |
| ROE | `INC_net / startYear_equity` | –1.53% | % |
| ROA [avg] | `currentYear_after_tax_operating_income / avg_total_assets` | 1.03% | % |
| ROC [avg] | `currentYear_after_tax_operating_income / avg_total_capitalization` | 2.07% | % |
| ROE [avg] | `INC_net / avg_equity` | –1.53% | % |

#### Efficiency

| Ratio | Formula | Value | Unit |
|-------|---------|-------|------|
| Asset Turnover (`RATIO_asset_turnover`) | `INC_sales / startYear_total_assets` | 0.596x | x |
| Receivables Turnover | `INC_sales / startYear_receivables` | 12.46x | x |
| Average Collection Period | `startYear_receivables / currentYear_daily_sales_average` | 29.3 days | days |
| Inventory Turnover | `INC_cost_goods_sold / startYear_inventory` | 55.4x | x |
| Days in Inventory | `startYear_inventory / currentYear_cost_goods_sold_daily` | 6.6 days | days |
| Gross Margin | `(INC_sales − INC_cost_goods_sold) / INC_sales` | 21.1% | % |
| Profit Margin | `INC_net / INC_sales` | –0.75% | % |
| Operating Profit Margin (`RATIO_operating_profit_margin`) | `currentYear_after_tax_operating_income / INC_sales` | 1.75% | % |

#### Leverage

| Ratio | Formula | Value | Unit |
|-------|---------|-------|------|
| Long-Term Debt Ratio | `currentYear_debt_long_term / (currentYear_debt_long_term + currentYear_equity)` | 43.1% | % |
| Long-Term Debt-Equity | `currentYear_debt_long_term / currentYear_equity` | 0.757x | x |
| Total Debt Ratio | `currentYear_liabilities_total / currentYear_assets_total` | 71.5% | % |
| Times Interest Earned (TIE) | `INC_ebit / INC_interest_expense` | 0.25x | x |
| Cash Coverage | `(INC_ebit + CASH_depreciation_amortization) / INC_interest_expense` | 14.83x | x |
| Debt Burden (`RATIO_debt_burden`) | `INC_net / currentYear_after_tax_operating_income` | –0.427x | x |
| Leverage Ratio (`RATIO_leverage`) | `currentYear_assets_total / currentYear_equity` | 3.508x | x |

> **Cash Coverage exception:** `INC_depreciation` = 0 because iQIYI embeds all D&A in cost of revenues (IS line). Cash Coverage uses `CASH_depreciation_amortization` = 13,264,120 (RMB '000) from the operating section of the Cash Flow Statement. This is the correct D&A figure for coverage purposes.

#### Liquidity

| Ratio | Formula | Value | Unit |
|-------|---------|-------|------|
| NWC to Assets | `currentYear_working_capital_net / currentYear_assets_total` | –25.2% | % |
| Current Ratio | `currentYear_assets_current / currentYear_liabilities_current` | 0.47x | x |
| Quick Ratio | `(currentYear_cash_marketable_securities + BAL_receivables_curr) / currentYear_liabilities_current` | 0.31x | x |
| Cash Ratio | `currentYear_cash_marketable_securities / currentYear_liabilities_current` | 0.20x | x |

#### Du Pont System

| Ratio | Formula | Value | Unit |
|-------|---------|-------|------|
| ROA (Du Pont) | `RATIO_asset_turnover × RATIO_operating_profit_margin` | 1.04% | % |
| ROE (Du Pont) | `RATIO_leverage × RATIO_asset_turnover × RATIO_operating_profit_margin × RATIO_debt_burden` | –1.56% | % |

---

### 7. Validation Rules

The executor must verify all checks below before proceeding to analysis.

| Check | Rule | Expected Result |
|-------|------|-----------------|
| Balance Sheet balance (FY2025) | `BAL_assets_total_curr = BAL_liabilities_total_curr + BAL_equity_shareholders_curr` | 46,681,735 = 46,681,735 ✓ |
| Balance Sheet balance (FY2024) | `BAL_assets_total_prior = BAL_liabilities_total_prior + BAL_equity_shareholders_prior` | 45,760,525 = 45,760,525 ✓ |
| Du Pont ROA check | `RATIO_asset_turnover × RATIO_operating_profit_margin = ROA (direct)` | 0.596 × 1.75% ≈ 1.04% ✓ |
| Du Pont ROE note | `ROE (Du Pont) ≠ ROE (direct)` by design | Mismatch source: `RATIO_leverage` uses `currentYear_equity` (FY2025 year-end) as its denominator, while direct ROE uses `startYear_equity` (FY2024 year-end). Both are internally consistent formulations; the 3-basis-point difference is a methodological artifact, not a computation error. Executor must explain this distinction — do not treat as a model error. |
| No formula errors | All Ratios tab cells resolve to numbers | No `#REF!`, `#DIV/0!`, `#NAME?` |
| Cash Coverage formula | Uses `CASH_depreciation_amortization`, not `INC_depreciation` | (229,315 + 13,264,120) / 909,616 = 14.83x |

---

## Part B — Analysis Specification

### 8. Analysis Requirements

The executor must address the following for each ratio category, using **FY2025 vs. FY2024 year-over-year comparison** as the benchmark. The Stage 3 workbook contains only FY2025 (current) and FY2024 (prior) data — do not reference FY2023 or any earlier period. No external peer comparison is required.

> **FY2024 IS scope note:** The Income Statement and Cash Flow Statement tabs contain FY2025 data only. FY2024 income statement absolute figures (net income, revenue, COGS, etc.) are **not** in the named-range model and must not be derived or cited. When referencing FY2024 profitability context, use only the margin percentages stated in §1 (net margin +2.7%, gross margin 24.9%). Do not back-calculate FY2024 absolute net income or revenue from these percentages.

**Performance (MVA, Market-to-Book, EVA):**
- Explain why MVA is deeply negative (market cap $1.1B USD vs. book equity RMB 13.3B ≈ $1.9B USD). Is this a valuation discount, a capital destruction signal, or both?
- EVA = –RMB 1.57B: quantify how much of this shortfall comes from insufficient operating income vs. high cost of capital applied to the capital base.
- Connect Market-to-Book < 1 to the retained-earnings deficit (–RMB 42.7B) and assess whether accumulated losses are the primary explanation.

**Profitability (ROA, ROC, ROE + margins):**
- Decompose the gap between operating profit margin (1.75%) and net profit margin (–0.75%). Identify interest expense (RMB 909.6M) as the bridge, and explain why operating income (EBIT = RMB 229.3M) is insufficient to cover it.
- Explain why ROA (1.04%) is positive while ROE (–1.53%) is negative — this is the leverage/interest burden story.
- Trend context: gross margin compressed from **24.9% (FY2024) → 21.1% (FY2025)** — a 380-basis-point year-over-year deterioration. All figures derivable from Stage 3 workbook: `(INC_sales − INC_cost_goods_sold) / INC_sales` for FY2025; `(BAL_assets_total_prior − BAL_liabilities_total_prior − ... )` — see Gross Margin definition in §6. Executor must not reference FY2023 data; it is not in the Stage 3 model and cannot be verified from spec inputs.

**Efficiency (turnover ratios):**
- Asset turnover (0.596x) declining trend signals that assets are not being converted to revenue efficiently as revenue falls. Connect to the fixed-cost nature of the content library.
- Inventory turnover (55.4x) and Days in Inventory (6.6 days) are very high — explain why for a content streaming company (licensed copyrights cycle quickly through amortization, not physical inventory).
- Average Collection Period (29.3 days) is reasonable; assess whether receivables quality is a concern given the macro advertising downturn.

**Leverage (debt ratios, TIE, coverage):**
- TIE = 0.25x is the critical red flag. EBIT covers only 25% of interest expense. Explain the solvency implication and connect to net loss.
- Cash Coverage = 14.83x (using D&A add-back). Contrast with TIE. Explain why the divergence is so large for a content platform (high non-cash D&A on content assets).
- Total Debt Ratio = 71.5%: contextualize against iQIYI's convertible note structure (PAG Notes $550M at 6%, 2028 Notes $208M at 6.5%).

**Liquidity:**
- Current Ratio = 0.47x and NWC = –RMB 11.8B are severe. Explain why a streaming company can operate with negative working capital (deferred revenue, content payables cycle).
- Cash Ratio = 0.20x: assess whether RMB 4.35B cash is sufficient given RMB 22.1B current liabilities.

---

### 9. Du Pont Decomposition

Decompose ROE using the four-factor Du Pont formula:

**ROE = Leverage × Asset Turnover × Operating Profit Margin × Debt Burden**

| Factor | Named Range | Value | Interpretation |
|--------|-------------|-------|----------------|
| Leverage | `RATIO_leverage` | 3.508x | High asset base relative to equity |
| Asset Turnover | `RATIO_asset_turnover` | 0.596x | Low revenue yield per RMB of assets |
| Operating Profit Margin | `RATIO_operating_profit_margin` | 1.75% | Thin operating margin after content costs |
| Debt Burden | `RATIO_debt_burden` | –0.427x | Interest expense drives net loss |
| **ROE (Du Pont)** | | **–1.56%** | |

**Instructions for executor:**
1. Identify which single factor most directly causes ROE to be negative: Debt Burden (–0.427x) flips the sign — isolate this as the primary driver.
2. Explain the time-mismatch: `RATIO_leverage` uses `currentYear_equity` (FY2025 year-end) as its denominator, while direct ROE uses `startYear_equity` (FY2024 year-end). This produces Du Pont ROE (–1.56%) ≠ direct ROE (–1.53%). Both formulations are internally consistent; the 3-basis-point difference is a methodological artifact, not a computation error. Do not treat as an error.
3. Assess whether leverage (3.508x) amplifies or masks the operational problem: with positive operating margin (1.75%), leverage should help ROE — but debt burden is negative, so leverage amplifies the loss instead.
4. Conclude: to restore positive ROE, iQIYI must first fix Debt Burden (reduce interest expense or grow operating income past the interest coverage threshold), then address Asset Turnover.

---

### 10. Strategic Recommendation Requirements

Produce **3–5 management-directed recommendations**. Each recommendation must:
- Name the specific ratio(s) that evidence the problem
- State the direction of required change (e.g., "reduce INC_interest_expense relative to INC_ebit")
- Be operationally specific (e.g., "accelerate repurchase of PAG Notes ahead of 2028 maturity rather than refinancing at current rates")
- Acknowledge one constraint or risk to executing the recommendation

**Minimum required topics:**
1. Interest coverage and debt restructuring (TIE = 0.25x is the most urgent signal)
2. Content cost strategy and gross margin recovery (operating margin 1.75% leaves zero buffer)
3. Asset productivity — how to improve asset turnover (0.596x) given fixed content library
4. Working capital management — current ratio 0.47x and NWC –RMB 11.8B
5. VIE/currency/regulatory risk management — the PAG Notes (USD 550M) and 2028 Notes (USD 208M) create USD-denominated debt obligations against primarily RMB operating cash flows; additionally, the VIE structure introduces PRC regulatory exposure (content bans, data-security reviews, HNTE license renewal risk) that is not captured by the model's WACC of 9% (which includes a 3% China risk premium). Recommendation must address how management should mitigate structural currency mismatch and what triggers would require immediate WACC reassessment.

---

### 11. Output Format

**Structure (in this exact order):**
1. Executive Summary (150–200 words): one-paragraph synthesis of financial condition. Lead with the TIE/debt burden finding.
2. Ratio Analysis by Category: one subsection per category (Performance, Profitability, Efficiency, Leverage, Liquidity). Each subsection: 150–250 words of prose. No raw ratio dumps — every number cited must be interpreted.
3. Du Pont Decomposition: present the four-factor table, then 200–300 words of factor-by-factor interpretation.
4. Strategic Recommendations: numbered list of 3–5 items. Each: 100–150 words with ratio evidence cited inline.
5. Appendix — Ratio Summary Table: one table listing all 25+ ratios with computed values and units. No interpretation required here.

**Language:** English only. All prose, headings, labels, and footnotes must be written in English regardless of the executor's default language.

**Tone:** MBA finance register. Precise, data-anchored, third-person. Avoid hedging phrases ("it could be argued that"). State findings directly.

**Length target:** 1,800–2,500 words excluding appendix table.

**Citation style:** Cite named ranges inline when referencing a specific figure (e.g., "`INC_ebit` = RMB 229,315K"). Cite the 20-F for any claim about business context.

**Unit translation rule:** All named-range values in this spec are in **RMB thousands (RMB '000)**. When presenting values in prose, convert to millions for readability: divide by 1,000 and append "M" (e.g., `INC_interest_expense` = 909,616 → **RMB 909.6M**; `currentYear_after_tax_operating_income` = 478,169 → **RMB 478.2M**). Do not write "RMB 478K" — that implies RMB 478,000, which is three orders of magnitude too small. Exception: small values below RMB 10M may be written as "RMB X,XXX K".

---

## References

iQIYI Inc. (2026). *Form 20-F: Annual Report for fiscal year ended December 31, 2025*. SEC EDGAR, CIK 0001722608.
URL: https://www.sec.gov/Archives/edgar/data/1722608/000119312526107079/iq-20251231.htm

BUS-629 Stage 1 Ratios Template. University of Hawaiʻi at Mānoa, Shidler College of Business.
URL: https://github.com/adamwstauffer/shidler/blob/main/docs/templates/spec-template.md

Stage 3 Populated Workbook: `models/builds/2026-05-14-phuong-iqiyi-financials.xlsx`
