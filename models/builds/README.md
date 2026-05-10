# models/builds/

Completed financial models with real data.

## Purpose

Stores working and completed models for each project or client. Each model is clearly named for easy traceability.

## Naming Convention

```
<model-type>_<project-name>_v<version>_<YYYYMMDD>.<ext>
```

Examples:
- `dcf_fptplay_v1_20260301.xlsx`
- `budget_forecast_fy2026_v2_20260415.xlsx`

## Model Status

| Status | Meaning |
|--------|---------|
| `draft` | Under development, not yet tested |
| `review` | Under review/approval |
| `final` | Approved and in official use |
| `archived` | Completed, kept for reference |
