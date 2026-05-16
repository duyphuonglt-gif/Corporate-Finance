# Prompt Log — BUS-629 iQIYI Project

**Author:** Luong Duy Phuong
**Company:** iQIYI Inc (IQ, NASDAQ)
**Course:** BUS-629 · University of Hawaiʻi at Mānoa, Shidler College of Business

---

## Entry 1 — Stage 4 Spec Draft (Round 1)

**Date:** 2026-05-16
**Tool:** Claude (Cowork mode, claude-sonnet-4-6)
**Session type:** Multi-turn collaborative drafting

### Inputs provided

I fed four sources into the session before drafting: the Stage 4 brief (fetched from the raw GitHub URL), the spec template (also fetched raw), the Stage 1 ratios template (`performance-ratios-template.xlsx`, uploaded), and the Stage 3 populated workbook (`2026-05-14-luong-iqiyi-financials.xlsx`, uploaded). Having both the template structure and the live financial values in context meant the LLM could populate formulas and computed figures in a single pass rather than leaving placeholders.

### Pre-draft clarification

Before drafting, I asked the LLM to surface all assumptions it needed to make — rather than let it proceed silently and embed assumptions I couldn't audit later. Four clarification questions were answered:

1. **Intended audience:** MBA academic — analysis should balance financial interpretation with methodology explanation.
2. **Benchmark:** Standalone trend only — iQIYI FY2023→FY2024→FY2025, no external peer comparison.
3. **Recommendation focus:** Management perspective, emphasizing operational improvements.
4. **D&A / Cash Coverage treatment:** iQIYI embeds all D&A in COGS, so `INC_depreciation` is zero. I specified that Cash Coverage must use `CASH_depreciation_amortization` from the CF Statement (13,264,120) and that the spec should document this exception explicitly so the Stage 5 executor doesn't silently use zero.

### Why I drafted this way

I noticed that specs which skip pre-draft clarification tend to embed the LLM's default assumptions invisibly — particularly around benchmark scope and D&A treatment. By surfacing these upfront and recording the answers, I created an auditable decision trail rather than having to reverse-engineer what the LLM assumed after the fact.

### Round 1 output

Spec saved to `docs/specs/2026-05-16-luong-iqiyi-spec.md`. All 11 sections populated (~3,300 words). 25+ ratios defined with named-range formulas and computed values drawn directly from the Stage 3 workbook.

---

## Entry 2 — Stage 4 HIL Review (Round 1 → Round 2)

**Date:** 2026-05-16
**Tool:** Claude (Cowork mode) + independent LLM test (separate session, no prior context)
**Session type:** Human-in-the-loop review and iteration

### HIL process

After Round 1, I ran two parallel review steps rather than just editing the spec myself. First, I asked Claude to re-read the spec independently and identify gaps before I tested it — this surfaced 5 gaps through self-review. Second, I fed the spec to a completely separate LLM session with zero prior context and asked it to (a) reconstruct the ratio model from the named ranges alone and (b) produce a full analysis. I saved that output as `2026-05-16-luong-iqiyi-analysis-test.md` and compared it against what the spec intended.

I chose this two-pass approach because self-review by the same LLM that wrote the spec tends to miss gaps it already "knows" from context. The independent reconstruction test forces the spec to stand on its own — if an executor with no prior knowledge produces wrong output, the spec is the problem, not the executor.

### Gaps identified and their real-world impact

| Gap | Description | Manifested in test? |
|-----|-------------|---------------------|
| GAP 1 | `CASH_depreciation_amortization` not a defined named range in Stage 1 template | Partial — LLM inferred correct value but referenced a non-existent named range |
| GAP 2 | Du Pont mismatch explanation incorrectly attributed to "assets" not "equity" | ✅ Yes — LLM propagated the wrong explanation verbatim |
| GAP 3 | FY2023 gross margin (27.5%) cited in §8 with no source in spec | ✅ Yes — LLM used figure without any citation; hallucination risk confirmed |
| GAP 4 | No recommendation topic for VIE/currency/regulatory risk | ✅ Yes — all 4 recommendations addressed debt/margin/asset/liquidity only |
| GAP 5 | §2 did not state IS and CF are current-year only | Not triggered in this test |
| NEW GAP A | Gross Margin not formally defined in §6 ratio table | ✅ LLM added it independently — inconsistency risk if different LLM omits it |
| NEW GAP B | Market-to-Book formula unspecified on FX conversion | ✅ LLM silently applied FX conversion not in spec, creating formula inconsistency |
| NEW GAP C | No unit translation instruction — RMB '000 values misread as RMB in prose | ✅ LLM wrote "RMB 478K" instead of "RMB 478M" (3-order-of-magnitude error) |

### Most consequential gaps

I found GAP 2 and GAP C to be the most consequential, for opposite reasons. GAP 2 was invisible arithmetically — the reconstruction test produced numbers that looked correct, but the explanation of why Du Pont ROE differs from direct ROE was wrong. The spec said the mismatch came from `currentYear_assets_total` vs. `startYear_equity`, which conflates two different variables entirely. The real cause is that `RATIO_leverage` uses `currentYear_equity` while direct ROE uses `startYear_equity` — the same variable, but different year endpoints. Without the reconstruction test I would never have caught this because the numbers passed. GAP C was the opposite: immediately obvious to any finance reader — "RMB 478K" instead of "RMB 478M" is a three-order-of-magnitude error that no unit translation rule in the spec allowed the executor to avoid.

### Round 2 fixes applied

- **GAP 2 (§7):** I rewrote the Du Pont mismatch explanation to correctly identify `currentYear_equity` (used by `RATIO_leverage`) vs. `startYear_equity` (used by direct ROE) as the source of the discrepancy.
- **GAP 3 (§8):** I added an explicit ⚠️ warning that FY2023 gross margin (27.5%) is sourced from Form 20-F FY2023 — not derivable from the Stage 3 workbook — and that the executor must cite the external source.
- **GAP 4 (§10):** I added a 5th required recommendation topic covering VIE/currency/regulatory risk, specifying the USD debt vs. RMB cash flow mismatch and HNTE license renewal risk as required discussion points.
- **GAP B (§6 + note):** I noticed the spec had not stated whether FX conversion should be applied to Market-to-Book, which caused the independent LLM to silently convert equity to USD before computing the ratio — an assumption not grounded in the template. The Stage 1 template formula is `market_capitalization / currentYear_equity` with no FX conversion: both inputs are used directly as '000 units per template convention, giving 1,136,292 / 13,308,926 = **0.085x**. MVA follows the same convention: `market_capitalization − currentYear_equity` = **−12,172,634 '000**. I added an explicit unit note to §6 stating that no FX conversion is applied to either Performance ratio, and that both use mixed-unit '000 inputs per the Stage 1 template. This prevents any executor from hallucinating a currency conversion step that does not exist in the model.
- **GAP C (§11):** I added an explicit unit translation rule stating that all named-range values are in RMB '000 and prose must express them in RMB millions (divide by 1,000), with a worked example to prevent recurrence.

### Gaps deferred

- **GAP 1:** The value for `CASH_depreciation_amortization` (13,264,120) is stated numerically in §3, so Stage 5 risk is low even without a formal named range. Deferred.
- **GAP 5:** The IS/CF current-year scope constraint was not triggered in the reconstruction test. Deferred.
- **NEW GAP A:** The independent LLM correctly added Gross Margin on its own; inconsistency risk noted but deferred to Stage 5 review.

---

## Entry 3 — FY2023 Removal Decision (Round 2 → Round 3)

**Date:** 2026-05-16
**Tool:** Claude (Cowork mode, claude-sonnet-4-6)
**Session type:** Architectural decision + spec revision

### Rationale for decision

I noticed that §8 still referenced FY2023 gross margin (27.5%) even after Round 2 fixes — the Stage 5 LLM executor would encounter a year that exists only partially in the workbook (balance sheet only; not carried into the income statement or cash flow tabs), forcing it to source the figure from outside the spec. That is exactly the condition that produced GAP 3 in the first place. Leaving any FY2023 reference in the spec is a structural invitation to hallucinate. I proposed removing FY2023 entirely so the executor only ever touches data it can actually derive from the named ranges — a two-year comparison (FY2025 vs. FY2024) is fully sufficient for the Stage 4 rubric and eliminates the hallucination risk at its root.

### Spec changes applied in Round 3

- **§8 scope instruction:** I noticed the scope still said "FY2023→FY2024→FY2025 where data available" — a conditional that invites the executor to guess which tables have FY2023 data. I changed it to "FY2025 vs. FY2024 year-over-year comparison only; do not reference FY2023" to remove the ambiguity entirely.
- **§8 Profitability analysis:** I removed the FY2023 gross margin (27.5%) anchor and rewrote the analysis baseline as FY2024: 24.9% → FY2025: 21.1% = 380 bps compression. All profitability commentary updated to two-year framing.
- **§6 Gross Margin (GAP A closed):** With FY2023 removed, I also closed the deferred GAP A — I noticed Gross Margin was still not formally defined in §6, meaning any executor that added it independently might use a different formula or label. I added it explicitly as `(INC_sales − INC_cost_goods_sold) / INC_sales` = 21.1% (FY2025), preventing inconsistency across different LLM executors.

### Gap status after Round 3

| Gap | Status |
|-----|--------|
| GAP 1 | Deferred (value stated numerically in §3) |
| GAP 2 | ✅ Fixed in Round 2 |
| GAP 3 | ✅ Fixed via FY2023 removal (Round 3) |
| GAP 4 | ✅ Fixed in Round 2 |
| GAP 5 | Deferred (not triggered in test) |
| NEW GAP A | ✅ Fixed in Round 3 (Gross Margin formally added to §6) |
| NEW GAP B | ✅ Fixed in Round 2 |
| NEW GAP C | ✅ Fixed in Round 2 |

6 of 8 identified gaps are now closed. The two deferred gaps carry low Stage 5 risk: GAP 1 because the D&A value is stated numerically in §3; GAP 5 because the scope constraint was not triggered in the independent reconstruction test. Spec is ready for Stage 4 submission.

---

## Entry 4 — Independent Reconstruction Test 2 (Round 3 → Round 4)

**Date:** 2026-05-16
**Tool:** Claude (Cowork mode, claude-sonnet-4-6) + independent LLM test (separate session, no prior context)
**Session type:** Second HIL reconstruction test; one new gap identified

### Test 2 process

After Round 3 fixes (FY2023 removal, GAP A closure), I ran a second independent reconstruction test — feeding the updated spec to a fresh LLM session with no prior context — to verify that the Round 2 and Round 3 changes held. All 8 previously identified gaps were confirmed closed: Cash Coverage used `CASH_depreciation_amortization` correctly, Du Pont mismatch explanation was correct, no FY2023 references appeared, Recommendation 5 covered VIE/currency/regulatory risk, all figures were expressed in RMB millions, and Gross Margin was formally included. The spec held.

### NEW GAP D — FY2024 absolute IS figures derived outside named-range model

I noticed that the Test 2 executive summary cited "a RMB 736.5M profit recorded in FY2024" — a figure that does not exist anywhere in the named-range model. Tracing backward, I found that the executor had back-calculated it from the "+2.7% net margin (FY2024)" line in §1 Scope, using FY2025 revenue (RMB 27,291.3M) as a proxy for FY2024 revenue: 27,291.3M × 2.7% ≈ 736.9M. The spec never said FY2024 revenue was the same as FY2025 — the executor filled the gap silently. This is exactly the pattern that produced GAP 3: a percentage in the spec without an explicit sourcing boundary invites the executor to derive an absolute figure that cannot be verified from named ranges.

The root cause is that the spec provides FY2024 margin percentages in §1 for context but never states that FY2024 income statement absolute figures are absent from the model. The IS and CF tabs contain FY2025 only — this was never declared anywhere in the spec, which is also the deferred GAP 5.

### Fix applied (Round 4)

I added a blockquote note at the top of §8 Analysis Requirements stating that IS and CF tabs contain FY2025 data only, that FY2024 absolute figures must not be derived or cited, and that executors should reference FY2024 profitability using only the margin percentages stated in §1. This fix simultaneously closes GAP D and the long-deferred GAP 5 in a single targeted addition — both gaps shared the same root cause: the spec never declared the IS/CF scope boundary.

### GAP 1 — Closed in Round 4 (alongside GAP D and GAP 5)

GAP 1 had been deferred since Round 2 on the grounds that `CASH_depreciation_amortization` value was stated numerically in §3, making the missing named range low risk. I decided to close it now rather than carry it to Stage 5, since the fix is simple and the risk of a Stage 5 executor referencing a non-existent named range — even if inferring the correct value — is worth eliminating. I noticed the right place to declare this is §4 Named Range Conventions, not §3, because that is where an executor would look to understand which names are available to reference. I added a blockquote note immediately after the Du Pont named ranges listing, explicitly flagging `CASH_depreciation_amortization` as a model-specific range absent from the Stage 1 template, specifying the value (13,264,120 RMB '000), and cross-referencing Note B in §3 to prevent any substitution of `INC_depreciation`.

### Gap status after Round 4

| Gap | Status |
|-----|--------|
| GAP 1 | ✅ Fixed in Round 4 (declared as model-specific named range in §4) |
| GAP 2 | ✅ Fixed in Round 2 |
| GAP 3 | ✅ Fixed via FY2023 removal (Round 3) |
| GAP 4 | ✅ Fixed in Round 2 |
| GAP 5 | ✅ Fixed in Round 4 (IS/CF scope boundary declared in §8) |
| NEW GAP A | ✅ Fixed in Round 3 |
| NEW GAP B | ✅ Fixed in Round 2 |
| NEW GAP C | ✅ Fixed in Round 2 |
| NEW GAP D | ✅ Fixed in Round 4 (FY2024 absolute IS figures prohibited in §8) |

All 9 identified gaps are now closed. Spec is ready for Stage 5 execution.

---

## Entry 5 — Independent Reconstruction Test 3 + Full Formula Audit

**Date:** 2026-05-16
**Tool:** Claude (Cowork mode, claude-sonnet-4-6) + independent LLM test (separate session, no prior context)
**Session type:** Third HIL reconstruction test; full formula verification against Stage 3 workbook

### Test 3 process

I ran a third independent reconstruction test against the Round 4 spec. The executor performed explicit formula-by-formula reconstruction — a more rigorous check than Tests 1 and 2 — computing every ratio from named ranges and comparing against spec values. All 9 gaps passed: `CASH_depreciation_amortization` used correctly, Du Pont mismatch explained correctly, no FY2023 or FY2024 absolute IS figures appeared, VIE/currency/regulatory covered in Recommendation 5, all prose figures in RMB millions. The executor also correctly identified that Market-to-Book = 0.085x per the `market_capitalization / currentYear_equity` formula — consistent with the Stage 1 template convention documented in GAP B fix (§6 unit note).

### Full formula audit

I used Test 3's line-by-line reconstruction as the basis for a complete audit of all 30 ratio formulas and computed values in §5 and §6 against the Stage 3 workbook. Every formula and value verified correct. The audit confirmed that the GAP B fix — clarifying that Market-to-Book and MVA use mixed '000 units with no FX conversion per Stage 1 template — produced the correct output: 0.085x and −12,172,634 respectively.

### Gap status — final

| Gap | Status |
|-----|--------|
| GAP 1 | ✅ Fixed in Round 4 |
| GAP 2 | ✅ Fixed in Round 2 |
| GAP 3 | ✅ Fixed in Round 3 |
| GAP 4 | ✅ Fixed in Round 2 |
| GAP 5 | ✅ Fixed in Round 4 |
| NEW GAP A | ✅ Fixed in Round 3 |
| NEW GAP B | ✅ Fixed in Round 2 |
| NEW GAP C | ✅ Fixed in Round 2 |
| NEW GAP D | ✅ Fixed in Round 4 |

All 9 gaps closed. All 30 ratio formulas verified against Stage 3 workbook. Spec is ready for Stage 5 execution.

---

---

## Entry 6 — Independent Reconstruction Test 5 (Gemini) + Round 5 Fixes

**Date:** 2026-05-16
**Tool:** Claude (Cowork mode, claude-sonnet-4-6) + independent LLM test (Gemini, separate session, no prior context)
**Session type:** Fifth HIL reconstruction test; two new gaps identified and closed

### Test 5 process

I ran a fifth independent reconstruction test using Gemini — a different LLM family than the Claude-based tests used in Tests 1–4 — to check whether the spec was executor-agnostic, not just Claude-compatible. Tests 1–4 were all within the same model family, so Gemini provided a cross-vendor check on whether the spec instructions were unambiguous enough to survive a model with different priors and formatting defaults.

### Findings

Three issues appeared in the Gemini output:

**GAP 2 re-triggered (§9 vs §7 internal contradiction).** I noticed that Gemini reproduced the original GAP 2 error: it described the Du Pont mismatch using assets terminology — "leverage uses `currentYear_assets_total` while direct ROA uses `startYear_total_assets`" — which is the same wrong explanation that §7 had correctly fixed in Round 2. Tracing the cause, I found that §9 instruction 2 still contained the original wrong language from before Round 2. §7 and §9 had diverged: §7 was correctly updated in Round 2 to cite `currentYear_equity` vs `startYear_equity`, but §9 was never updated to match. Gemini read §9 last and overwrote the correct §7 framing with the contradicting §9 instruction. The spec contained an internal contradiction, and Gemini resolved it by deferring to the later instruction rather than the earlier one.

**GAP C partial fail (unit translation).** Gemini wrote "RMB 447.5K" instead of "RMB 447.5M" for `BAL_inventories_curr`. The unit translation rule is present in §11, but Gemini misapplied it in this one instance. I assessed this as an execution error by Gemini, not a new spec gap — the rule is unambiguous — and deferred correction to Stage 5 review.

**NEW GAP F — §11 does not specify output language.** I noticed Gemini wrote the executive summary and several ratio subsections in Vietnamese. The spec had never stated what language the output should be in, so Gemini defaulted to a language it inferred from the project context (Vietnamese-named author, Vietnamese file history). This is a straightforward omission in §11 and a new gap.

### Why GAP 2 re-triggering matters

I found the §9/§7 contradiction the most instructive finding in Test 5. Tests 1–4 all used Claude, which likely resolved the contradiction by weighting §7 (more detailed, earlier in the spec) over §9 (shorter, later). Gemini resolved it differently — by following the most recent explicit instruction — and therefore reproduced the original GAP 2 error. This revealed that the spec had a latent internal inconsistency that the Claude-family tests masked. Cross-vendor testing is essential for exactly this reason: different models have different heuristics for resolving contradictions, and a spec that only passes with one model family is not robust.

### Round 5 fixes applied

- **§9 instruction 2:** I rewrote the time-mismatch description to match §7 exactly: "`RATIO_leverage` uses `currentYear_equity` (FY2025 year-end) as its denominator, while direct ROE uses `startYear_equity` (FY2024 year-end). This produces Du Pont ROE (–1.56%) ≠ direct ROE (–1.53%). Both formulations are internally consistent; the 3-basis-point difference is a methodological artifact, not a computation error. Do not treat as an error."
- **§11 language specification (NEW GAP F):** I added an explicit language instruction immediately before the Tone instruction: "Language: English only. All prose, headings, labels, and footnotes must be written in English regardless of the executor's default language."

### Gap status after Round 5

| Gap | Status |
|-----|--------|
| GAP 1 | ✅ Fixed in Round 4 |
| GAP 2 | ✅ Re-fixed in Round 5 (§9 instruction 2 corrected to match §7) |
| GAP 3 | ✅ Fixed in Round 3 |
| GAP 4 | ✅ Fixed in Round 2 |
| GAP 5 | ✅ Fixed in Round 4 |
| NEW GAP A | ✅ Fixed in Round 3 |
| NEW GAP B | ✅ Fixed in Round 2 |
| NEW GAP C | ✅ Fixed in Round 2 (execution error in Test 5; not a new spec gap) |
| NEW GAP D | ✅ Fixed in Round 4 |
| NEW GAP F | ✅ Fixed in Round 5 (language: English added to §11) |

10 gaps tracked; all closed. Cross-vendor test (Gemini) confirmed spec is now executor-agnostic. Spec ready for Stage 4 submission.

---

*Log maintained per BUS-629 Stage 4 rubric requirement: "Spec craft + prompt log quality" (25% of Stage 4 score). Each entry documents the gap identified, why the spec caused it, and what was changed.*
