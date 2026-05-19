# Prompt Log

## Prompt 1

I received an email from YipitData about an AI Engineer take-home assessment. I provided the email and attached files, then asked Codex to deeply analyze what the task is and explain it before starting.

## Prompt 2

I asked Codex to implement the plan: create `analysis.ipynb`, `memo.md`, `prompts.md`, and a `data/` folder; align FRED retail sales to Walmart fiscal quarters; evaluate out-of-sample forecasts against naive baselines; include lag sensitivity, clean figures, caveats, and a concise memo.

## Prompt 3

After the first draft, I asked Codex to deeply re-analyze the assignment and modify anything that would make the submitted solution stronger.

## Prompt 4

I asked Codex to assume the role of a senior data scientist reviewing the assignment, add anything expected of a top candidate, polish the submission, and prepare it for git.

## Note on LLM usage

Codex was most useful for keeping the work focused on the customer's actual decision: out-of-sample forecast improvement versus a real baseline, not an in-sample regression. I checked the assistant's output by inspecting raw date ranges, verifying Walmart fiscal-quarter alignment, and running the notebook code cells end to end. Two issues I corrected rather than accepting blindly: same-quarter retail sales is more of a nowcast than a clean leading signal, and the initial notebook used a Matplotlib style unavailable in my local environment. I also added paired diagnostics and bootstrap uncertainty so the conclusion did not rest only on aggregate MAPE.
