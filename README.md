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
- `models/churn_pipeline.pkl`: the Milestone 3 model artifact. If it is absent,
  the script creates a deterministic fallback model so the repository can be
  executed from a clean checkout.

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
`strain_col`. The model should expose a scikit-learn-compatible
`predict_proba(X)` method; if prediction fails, the generator uses a bounded
strain-based estimate for visualization rather than stopping report creation.

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
- [ ] `models/churn_pipeline.pkl` is present after execution.
- [ ] `reports/executive_summary.md` is generated.
- [ ] `reports/churn_heatmap.png` is generated and includes regional labels.
- [ ] The summary contains the required ROI, CFO quote, and ethical warning.