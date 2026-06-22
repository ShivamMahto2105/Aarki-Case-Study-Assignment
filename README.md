# Lead Quality Case Study — Analyst Assessment

## Overview
This project analyzes a lead-generation dataset for a single publisher selling leads (across multiple traffic sources) to one advertiser in the debt-settlement space. It answers three core business questions and provides a data-driven recommendation on whether a proposed **+20% CPL increase ($30 → $33)** is justified by lead-quality improvements.

**Dataset:** `Analyst_case_study_dataset.xls` — leads generated April–September 2009.

## Business Questions
1. Is lead quality trending up, down, or flat over time — and is any trend statistically significant?
2. What segments (widget/creative, partner, debt level, state, keyword, ad group, referral domain) drive differences in lead quality?
3. Can targeting/filtering realistically deliver a +20% quality lift to justify a +20% CPL?

## Methodology
- **Data cleaning:** deduplicated on `VendorLeadID`, dropped unkeyed rows, parsed dates, filled non-critical categorical nulls with "Unknown", and dropped PII columns (`IP Address`, `FirstName`, `Email`).
- **QA finding:** corrected a status-text matching bug that was silently misclassifying 297 "Bad" leads as "Unknown" due to apostrophe/prefix mismatches in `CallStatus`.
- **Feature engineering:** built a `QualityGroup` (Closed / Good / Bad / Unknown) from `CallStatus`, plus `GoodLeadFlag`, `ClosedLeadFlag`, `Month`, and `Quarter`.
- **Statistical testing:** linear regression for trend significance; chi-square tests of independence for all categorical drivers (α = 0.05).
- **Scenario modeling:** simulated five lead-filtering strategies (drop weak widgets, drop weak campaigns, target top states, gate on data-quality score, and a combined approach) against the +20% quality target.

## Key Findings

| Question | Finding |
|---|---|
| **Q1 — Trend** | Flat. Slope +0.45 pts/month, p = 0.587, R² = 0.08 — not statistically significant. |
| **Q2 — Drivers** | Statistically significant: **Widget/creative** (p=0.026), **Partner** (p=1e-05), **Debt Level** (p=2e-05), **Keyword** (p=0.001), **AdGroup** (p≈0). Not significant: State, PublisherCampaignName, AdvertiserCampaignName, ReferralDomain. |
| **Q3 — CPL justification** | Baseline Good+Closed rate = 12.95%; +20% target = 15.54%. Achievable: dropping weak widgets alone reaches the target; combining widget + campaign + state filters reaches 22.58%. |

### Notable Segment Insights
- **Best widget:** `w-300250-DebtReduction1-1DC-CreditSolutions` (36.84% quality score) vs. worst `...-Head3` (12.0%).
- **Best partner:** AdKnowledge (30.99%) vs. weakest `Google` paid tag (11.17%) — a 19+ point gap.
- **Best debt segments:** $70,001–$90,000 and $10,001–$15,000 ("sweet spot" debt ranges).
- **Biggest blind spot:** 71% of leads carry an "Unknown" outcome status — the single largest opportunity to sharpen every other metric.

## Recommendations
- **Quick wins:** Reallocate spend away from bottom-half widgets; fix call-center workflow to resolve every lead's status within an SLA window.
- **Medium-term:** Shift budget toward AdKnowledge traffic and away from weak `Google` paid traffic; resolve `google`/`Google` tagging inconsistency with ad ops; build state-tier targeting.
- **Long-term:** Negotiate the +20% CPL increase backed by the combined-scenario evidence (up to 22.58% achievable quality), with the caveat that the 71% Unknown bucket should be resolved first to validate findings on full data.

## Repository Contents
| File | Description |
|---|---|
| `Lead_Quality_Analysis.ipynb` | Full analysis notebook (data cleaning, EDA, statistical tests, scenarios) |
| `Analyst_case_study_dataset.xls` | Source dataset (not included here — supply separately) |
| `Lead_Quality_Analysis.pptx` | Executive presentation summarizing findings |

## How to Run
```bash
pip install pandas numpy matplotlib seaborn scipy xlrd
jupyter notebook Lead_Quality_Analysis.ipynb
```
Ensure `Analyst_case_study_dataset.xls` is in the same working directory before running.

## Tools Used
Python, pandas, numpy, matplotlib, seaborn, scipy (linregress, chi2_contingency)
