# Phase 4: Actionable Business Translation

This project turns churn model outputs into boardroom-ready actions for
NexusLend. It pairs projected financial value with inclusion measures so that
customer support recommendations can be evaluated for both business impact and
human impact.

## What It Produces

Running `report_generator.py` creates:

- `reports/executive_summary.md`: a leadership summary organized as Problem,
  Solution, ROI, and Next Steps.
- `reports/churn_heatmap.png`: mean predicted churn probability by region and
  financial-strain quartile, with an ethical disclaimer displayed on the chart.
- `models/churn_pipeline.pkl`: the original Milestone 3 model artifact. The
  script refuses to run if this file is absent or invalid; it never creates a
  replacement model.
- The model must be the bias-aware model trained in phase 3, including the
  fairness work completed during model training. Set `MILESTONE3_MODEL` only
  when the authentic artifact is stored under a different path; the default
  remains `models/churn_pipeline.pkl` for assignment grading.

## Setup

Python 3.9 or newer is recommended. Install the project dependencies from the
repository root:

```bash
python -m pip install -r requirements.txt
```

The dependencies are `pandas` for tabular transformations, `matplotlib` for
image output, and `seaborn` for the heatmap visualization.

## Run

Execute the generator from the repository root:

```bash
python report_generator.py
```

The command is non-interactive and writes all deliverables beneath `reports/`.
It also prints a compact status message, for example:

```text
Generated 3 interventions | Projected ROI: R2.1M
```

## ReportGenerator API

`ReportGenerator` accepts a model and a pandas DataFrame:

```python
from report_generator import ReportGenerator

generator = ReportGenerator(model, X)
generator.calculate_intervention_roi()
generator.generate_churn_heatmap("reports/churn_heatmap.png")
generator.generate_executive_summary("reports/executive_summary.md")
```

The DataFrame must contain `region` and `financial_strain` columns for the
heatmap. Custom column names can be supplied with `region_col` and
`strain_col`. The model must expose a scikit-learn-compatible
`predict_proba(X)` method. Prediction failures are not silently converted into
synthetic model outputs.

When the script is run directly, it loads engineered/model-training data from
the path in `MILESTONE2_DATA`, or from one of these repository paths:
`data/engineered_features.csv`, `data/engineered_dataset.csv`,
`data/X_engineered.csv`, `data/model_dataset.csv`,
`data/churn_model_dataset.csv`, `data/processed/engineered_features.csv`, or
`data/processed/model_dataset.csv`. The data must be copied from the earlier
milestones and include `region` and `financial_strain`; the script does not
create demo rows.

## Phase 3 Inputs

The phase 4 report depends on these phase 3 assets being committed to the
repository:

```text
models/churn_pipeline.pkl              # bias-aware trained pipeline
data/engineered_features.csv           # engineered features used by the model
data/model_dataset.csv                 # optional name for the same model dataset
```

Only the dataset path that exists is loaded. Keep the actual phase 3 training
dataset and bias-aware pipeline together with the report code so the heatmap
and executive conclusions can be reproduced and audited. A missing model or
dataset causes a clear error rather than producing a synthetic report.

## ROI Methodology

The generator evaluates three opt-in interventions:

1. **Fee Waiver for High-Strain**: R185,000 cost, 320 projected saved
	customers, and R6,500 lifetime value per saved customer.
2. **Load-Shedding Support Package**: R300,000 cost, 75 projected saved
	customers, and R5,500 lifetime value per saved customer.
3. **Flexible Payment Plan**: R140,000 cost, 35 projected saved customers,
	and R5,500 lifetime value per saved customer.

For each action:

```text
gross value = saved customers * lifetime value
net impact = gross value - intervention cost
ROI = net impact / intervention cost
```

Results are returned in descending order of net impact. Each intervention also
includes `inclusion_metrics`, including the number of underserved customers
reached and their share of eligible reach. The figures are planning estimates,
not guarantees; a pilot should replace them with observed outcomes.

## Ethical Guardrails

Every recommendation and the heatmap explicitly includes this non-negotiable
rule:

> Do NOT target high-strain customers with predatory offers

Interventions must be affordable, transparent, opt-in, and available without a
penalty for declining. Monitoring should include complaints, repayment
outcomes, uptake, and reach in underserved regions, rather than relying on
aggregate profit alone.

The executive summary also includes these required governance triggers and
stakeholder perspectives:

- `Retrain if township recall drops >10%.`
- `Per CFO: 'Every 1% churn reduction = R420k saved'`
- A Head of Inclusion perspective on measuring outcomes in underserved regions.

## Milestone 4 Submission Checklist

- [ ] Repository is public.
- [ ] `report_generator.py` is in the repository root.
- [ ] The original bias-aware Milestone 3 `models/churn_pipeline.pkl` is present before execution.
- [ ] Milestone 2 engineered/model-training data is present, or `MILESTONE2_DATA` points to it.
- [ ] `reports/executive_summary.md` is generated.
- [ ] `reports/churn_heatmap.png` is generated and includes regional labels.
- [ ] The summary contains the required ROI, CFO quote, and ethical warning.