# data/

Raw and processed data for financial models and analysis.

## Purpose

Stores raw data, processed data, and reference data used for model building.

## Recommended Structure

```
data/
  raw/          # Source data, do not modify
  processed/    # Cleaned and standardized data
  reference/    # Reference data: interest rates, exchange rates, benchmarks
  external/     # Third-party data (Bloomberg, Vietstock, etc.)
```

## Notes

- Do not commit sensitive data (unpublished internal figures) to repo.
- Document data source and collection date in filename or metadata.
- Preferred formats: `.csv`, `.xlsx`, `.json`.
