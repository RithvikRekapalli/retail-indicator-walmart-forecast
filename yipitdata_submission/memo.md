# Memo: Should FRED retail sales lead Walmart revenue?

**Recommendation:** I would not use the supplied FRED retail-sales series as a standalone leading indicator for Walmart revenue. It beats a very weak "same quarter last year" baseline, but it does not beat the stronger naive benchmark I would actually rely on: same quarter last year plus Walmart's most recently observed YoY growth.

I aligned FRED monthly retail sales to Walmart's fiscal quarters, which end in January, April, July, and October. A normal calendar-quarter resample would be wrong; it produces zero date matches against Walmart's fiscal-quarter revenue file. I then ran an expanding-window out-of-sample test over 35 quarters from 2017-07-31 through 2026-01-31.

| Model | MAPE | MAE |
|---|---:|---:|
| Last observed Walmart YoY growth | 1.94% | $2.95B |
| Retail YoY + last observed Walmart YoY | 2.23% | $3.34B |
| Historical average Walmart YoY growth | 2.26% | $3.44B |
| Same-quarter retail YoY | 2.64% | $3.98B |
| Prior-quarter retail YoY | 2.88% | $4.31B |
| Same quarter last year | 4.16% | $6.31B |

The answer depends on the baseline. If the benchmark is simply last year's same quarter, retail looks helpful. But versus the best naive baseline, the same-quarter retail model has 36% higher MAPE and the stricter one-quarter-leading version has 49% higher MAPE. Adding lagged retail sales to Walmart's own latest YoY trend still loses by 15% on MAPE.

This is not just an aggregate-error artifact. The strict lagged retail model beats the best naive baseline in only 14 of 35 quarters, and its average absolute error is about $1.37B worse. The combined retail-plus-trend model wins slightly more often, 18 of 35 quarters, but its losses are larger than its wins. That is not a signal I would pay for without more evidence.

A paired bootstrap over the 35 forecast quarters points the same way. The strict lagged retail model has only about a 1.5% bootstrap probability of beating the best naive baseline on average MAPE; the combined retail-plus-trend model is better but still only about 11%. The sample is small, so I would not over-read exact p-values, but the direction is not encouraging.

The result makes business sense. Walmart's own recent growth is much more informative than broad retail sales in this sample: lagged Walmart YoY growth correlates about 0.61 with current Walmart YoY growth, while FRED retail YoY is only about 0.05 contemporaneously and 0.10 with a one-quarter lag. Broad retail sales misses Walmart-specific drivers like grocery mix, share gains, pricing, ecommerce, fuel, and execution.

The main worries are timing, revisions, and stability. Same-quarter FRED data may be useful as an earnings-season nowcast, but that is weaker than the customer's "leading indicator" language. A stricter prior-quarter test performs worse. The sample is small, FRED data can be revised, and the pandemic period creates regime-risk.

**What would change my mind:** a real-time test using only FRED vintages available before each Walmart report, at least a 10% out-of-sample MAPE improvement versus the last-YoY baseline, and evidence that the improvement survives non-pandemic periods or comparable retailers. Until then, I would treat FRED retail sales as context, not a primary predictor.
