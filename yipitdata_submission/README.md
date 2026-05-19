# YipitData AI Engineer Take-Home Submission

This folder contains a complete, reproducible answer to the take-home question:

> We track the monthly retail-sales series from FRED for our subsector. We are thinking about using it as a leading indicator of quarterly revenue for Walmart. Does it predict Walmart's revenue better than a naive baseline? If yes, by how much, and what should we worry about? If no, what evidence would change our minds?

## Short Answer

On the supplied data, I would **not** use the FRED retail-sales series as a standalone leading indicator for Walmart revenue.

It beats the weakest baseline, "same quarter last year," but it does not beat the stronger naive baseline I would actually use: same quarter last year plus Walmart's most recently observed YoY growth.

Headline out-of-sample result:

| Model | MAPE | MAE |
|---|---:|---:|
| Last observed Walmart YoY growth | 1.94% | $2.95B |
| Retail YoY + last observed Walmart YoY | 2.23% | $3.34B |
| Historical average Walmart YoY growth | 2.26% | $3.44B |
| Same-quarter retail YoY | 2.64% | $3.98B |
| Prior-quarter retail YoY | 2.88% | $4.31B |
| Same quarter last year | 4.16% | $6.31B |

## Repository Contents

```text
yipitdata_submission/
├── analysis.ipynb
├── memo.md
├── prompts.md
├── README.md
├── requirements.txt
└── data/
    ├── retail_sales_fred.csv
    └── walmart_revenue.csv
```

File descriptions:

- `analysis.ipynb`: full notebook analysis. It includes saved outputs and can be rerun end to end.
- `memo.md`: one-page, portfolio-manager-facing recommendation memo.
- `prompts.md`: prompt log plus a short note on how LLM output was checked.
- `README.md`: this file.
- `requirements.txt`: minimal Python packages needed for the analysis.
- `data/retail_sales_fred.csv`: supplied monthly FRED retail-sales data.
- `data/walmart_revenue.csv`: supplied quarterly Walmart revenue data.

## Methodology Summary

The notebook does the following:

1. Loads the supplied CSVs directly from `data/`.
2. Aligns monthly FRED retail sales to Walmart fiscal quarters.
3. Drops the first incomplete aligned quarter.
4. Computes Walmart revenue YoY growth and FRED retail-sales YoY growth.
5. Runs expanding-window out-of-sample forecasts.
6. Compares the FRED signal against naive baselines.
7. Tests a stricter leading version using prior-quarter FRED retail sales.
8. Adds paired diagnostics and a bootstrap uncertainty check versus the best naive baseline.
9. Checks robustness around the pandemic period.
10. Produces a forecast-comparison figure and a MAPE-comparison figure.

Important alignment detail:

Walmart fiscal quarters end in **January, April, July, and October**, not March, June, September, and December. A normal calendar-quarter resample would be wrong here. The notebook includes a sanity check showing that a calendar-quarter resample produces `0` matched rows against the Walmart quarter-end dates.

## Key Modeling Choices

Baselines:

- `seasonal_naive`: same quarter last year.
- `historical_avg_yoy`: same quarter last year plus average historical Walmart YoY growth observed so far.
- `last_yoy`: same quarter last year plus the most recently observed Walmart YoY growth.

Retail-signal models:

- `retail_same_q`: same-quarter FRED retail YoY. This is closer to a nowcast, because same-quarter FRED data may be available before Walmart reports but is not a clean long-lead signal.
- `retail_lag1`: prior-quarter FRED retail YoY. This is the stricter leading-indicator test.
- `retail_lag1_plus_last_yoy`: prior-quarter FRED retail YoY added to Walmart's own latest YoY trend.

Evaluation:

- Expanding-window time-series split.
- No shuffled cross-validation.
- Each forecast is trained only on data available before that forecast date.
- Main metrics: MAPE, MAE, RMSE, and bias.

## Python Requirements

The analysis uses only common Python packages:

```text
pandas
numpy
matplotlib
ipykernel
```

The notebook can be viewed without installing anything if your notebook viewer supports saved outputs. To rerun it, install the packages in `requirements.txt`.

`ipykernel` is included so notebook editors such as VS Code can run the notebook from a virtual environment.

## How to Run on macOS or Linux

Open a terminal and move into the submission folder:

```bash
cd yipitdata_submission
```

Create and activate a virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate
```

If your system uses `python3` instead of `python`, use:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Install dependencies:

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

Then open `analysis.ipynb` in VS Code, JupyterLab, Jupyter Notebook, or another notebook editor and run all cells.

## How to Run on Windows PowerShell

Open PowerShell and move into the submission folder:

```powershell
cd yipitdata_submission
```

Create and activate a virtual environment:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

Install dependencies:

```powershell
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Then open `analysis.ipynb` in VS Code, JupyterLab, Jupyter Notebook, or another notebook editor and run all cells.

If PowerShell blocks activation scripts, either approve the local project activation according to your organization policy or use a notebook environment that already has the packages installed.

## Running in VS Code

1. Open VS Code.
2. Choose **File > Open Folder...**.
3. Open the `yipitdata_submission` folder.
4. Open `analysis.ipynb`.
5. Click the kernel picker in the top-right corner of the notebook editor.
6. Choose the interpreter inside `.venv`.
   - macOS/Linux path ends with `.venv/bin/python`.
   - Windows path ends with `.venv\Scripts\python.exe`.
7. Click **Run All**.

If VS Code asks to install `ipykernel`, confirm that the selected interpreter is the `.venv` interpreter. If it is not, cancel the popup and select the `.venv` interpreter manually.

## Running in JupyterLab or Jupyter Notebook

If Jupyter is already installed on your system, activate the virtual environment and start it from this folder:

```bash
source .venv/bin/activate
jupyter lab
```

or:

```bash
source .venv/bin/activate
jupyter notebook
```

On Windows PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
jupyter lab
```

If Jupyter is not installed, install it after activating the virtual environment:

```bash
pip install jupyterlab
```

Then open `analysis.ipynb` and run all cells.

## How to Know It Ran Successfully

A successful run should show:

1. No red traceback/error cells.
2. Code cells numbered in order, not stuck at `[*]`.
3. An alignment check that prints:

```text
Calendar-quarter resample matched rows: 0
```

4. A metrics table where the best naive baseline is:

```text
last_yoy    MAPE about 1.94%
```

5. A stricter leading retail model result around:

```text
retail_lag1    MAPE about 2.88%
```

6. Two generated figures:

```text
figures/forecast_comparison.png
figures/mape_comparison.png
```

The notebook also includes saved outputs and embedded figures, so the main result is visible before rerunning.

## Expected Interpretation

The FRED retail-sales signal does not clear the most relevant benchmark.

The strict leading model, `retail_lag1`, is worse than the best naive baseline:

- It wins only 14 of 35 quarters versus `last_yoy`.
- Its average absolute error is about $1.37B worse.
- A paired bootstrap gives it only about a 1.5% probability of beating `last_yoy` on average MAPE.
- It is especially weak around some high-volatility periods.

The combined model, `retail_lag1_plus_last_yoy`, wins slightly more often quarter by quarter, but its losses are larger than its wins, so it still loses on MAPE. Its bootstrap probability of beating `last_yoy` is only about 11%.

## Main Caveats

- The sample is small: quarterly data from 2010 to early 2026.
- FRED values can be revised; this analysis uses the supplied final CSV, not vintage real-time releases.
- Same-quarter FRED data is closer to a nowcast than a clean leading signal.
- Walmart-specific drivers, such as grocery mix, market-share gains, fuel, ecommerce, pricing, and execution, are not captured by a broad retail-sales series.
- Pandemic-era behavior may not generalize.

## Evidence That Would Change the Conclusion

The conclusion would become more positive if a future test showed:

- real-time FRED vintages available before each Walmart report;
- at least a 10% out-of-sample MAPE improvement versus `last_yoy`;
- robustness outside pandemic-distorted periods;
- similar improvement across comparable retailers or a more Walmart-specific subsector signal.

## Troubleshooting

If `analysis.ipynb` cannot find the CSV files:

- Make sure you opened or ran the notebook from the `yipitdata_submission` folder.
- Confirm the `data/` folder exists next to `analysis.ipynb`.

If VS Code says `ipykernel` is missing:

- Make sure the selected notebook kernel is the virtual environment you created.
- Install `ipykernel` inside that same environment:

```bash
pip install -r requirements.txt
```

If a chart does not display interactively:

- The notebook still saves charts into `figures/`.
- Check `figures/forecast_comparison.png` and `figures/mape_comparison.png`.

If the reviewer does not want to rerun the notebook:

- The notebook already contains saved output tables and embedded charts.
- The `memo.md` file contains the concise decision summary.
