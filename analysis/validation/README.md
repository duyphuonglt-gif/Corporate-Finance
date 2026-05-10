# analysis/validation/

Model accuracy and data validation.

## Purpose

Ensures models have no formula errors, input data matches source documents, and results are reasonable before use in decision-making.

## Standard Validation Checklist

- [ ] Balance sheet balances (Assets = Liabilities + Equity)
- [ ] Cash flow reconciles with adjusted earnings
- [ ] No unintended circular references in formulas
- [ ] Historical data matches audited financial statements
- [ ] DCF results within reasonable range vs. market comparables

## Naming Convention

```
validation_<model-name>_<YYYYMMDD>.md
```

Examples:
- `validation_dcf_fptplay_20260315.md`
