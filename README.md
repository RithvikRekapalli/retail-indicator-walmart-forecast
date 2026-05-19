# Retail Indicator Walmart Forecast

This repository contains a complete YipitData AI Engineer take-home submission analyzing whether the monthly FRED retail-sales series is useful as a leading indicator for Walmart quarterly revenue.

## Short Answer

I would **not** use the supplied FRED retail-sales series as a standalone leading indicator for Walmart revenue.

It beats a weak "same quarter last year" baseline, but it does **not** beat the stronger naive benchmark I would actually rely on: same quarter last year plus Walmart's most recently observed YoY growth.

Headline out-of-sample result:

| Model | MAPE | MAE |
|---|---:|---:|
| Last observed Walmart YoY growth | 1.94% | $2.95B |
| Retail YoY + last observed Walmart YoY | 2.23% | $3.34B |
| Historical average Walmart YoY growth | 2.26% | $3.44B |
| Same-quarter retail YoY | 2.64% | $3.98B |
| Prior-quarter retail YoY | 2.88% | $4.31B |
| Same quarter last year | 4.16% | $6.31B |

## Files

The submission package is in [`yipitdata_submission/`](./yipitdata_submission/):

- [`analysis.ipynb`](./yipitdata_submission/analysis.ipynb): full reproducible notebook with saved outputs and figures.
- [`memo.md`](./yipitdata_submission/memo.md): one-page portfolio-manager-facing recommendation memo.
- [`prompts.md`](./yipitdata_submission/prompts.md): prompt log and LLM validation note.
- [`README.md`](./yipitdata_submission/README.md): detailed reviewer/run instructions.
- [`requirements.txt`](./yipitdata_submission/requirements.txt): Python dependencies.
- [`data/`](./yipitdata_submission/data/): supplied CSV inputs.

A zipped copy for direct submission is also included:

- [`yipitdata_submission.zip`](./yipitdata_submission.zip)

## How to Run

From the repository root:

```bash
cd yipitdata_submission
python -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

On Windows PowerShell:

```powershell
cd yipitdata_submission
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Then open `analysis.ipynb` in VS Code, JupyterLab, Jupyter Notebook, or another notebook editor and run all cells.

The notebook should produce:

- `Calendar-quarter resample matched rows: 0`
- best naive baseline MAPE around `1.94%`
- strict lagged retail model MAPE around `2.88%`
- generated figures under `yipitdata_submission/figures/`

For full methodology, caveats, troubleshooting, and reviewer notes, see [`yipitdata_submission/README.md`](./yipitdata_submission/README.md).
