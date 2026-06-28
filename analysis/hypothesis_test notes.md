# Hypothesis Test Notes

## Task 6 — Hypothesis Framing

| Element | Detail |
|---|---|
| **Metric being tested** | Paid conversion rate (`converted_to_paid` / total users) |
| **Reason for choosing this metric** | It's the North Star — the only funnel metric that reflects actual revenue-generating outcomes rather than progress toward them (see README Section 3 for full reasoning) |
| **Null hypothesis (H0)** | The new campaign (Treatment) does not increase the paid conversion rate compared to the existing experience (Control). p_treatment = p_control |
| **Alternate hypothesis (H1)** | The new campaign (Treatment) increases the paid conversion rate compared to Control. p_treatment > p_control |
| **One-tailed or two-tailed** | **One-tailed.** The business question is specifically "does the new campaign do better?" — leadership isn't going to launch a campaign that performs *worse*, so the only actionable direction is an improvement. A two-tailed test would be appropriate if the business cared equally about detecting a decline, which isn't the framing here. |
| **Significance level (α)** | 0.05 |
| **Interpretation logic** | If p-value < 0.05, reject H0 and conclude Treatment produces a real improvement in paid conversion rate. If p-value ≥ 0.05, fail to reject H0 — treat any observed lift as not distinguishable from random variation, and do not launch on this evidence alone. |

**Connection to the business decision:** if H0 is rejected, that's the primary evidence
needed to recommend launching the campaign (combined with a guardrail check — Task 8).
If H0 is not rejected, the recommendation defaults to "continue testing" rather than
launch, since there isn't yet statistical evidence the campaign improves the metric
leadership cares about.

## Task 7 — Hypothesis / A-B Test Analysis

### Summary of Test Inputs

| Group | Users (n) | Converted (x) | Conversion rate |
|---|---|---|---|
| Control | 690 | 22 | 3.19% |
| Treatment | 710 | 50 | 7.04% |

**Test used:** Two-proportion z-test (large-sample approximation; appropriate here since
both np and n(1-p) are well above 5 for both groups).

### Test Output

```
p1 (Control)    = 22/690  = 0.0319
p2 (Treatment)  = 50/710  = 0.0704
Pooled p        = 72/1400 = 0.0514
Standard error  = sqrt[ p(1-p) * (1/n1 + 1/n2) ] = 0.01181
Z-statistic     = (p2 - p1) / SE = 3.264
One-tailed p-value = 0.00055
95% CI for the absolute difference (p2 - p1) = 1.56pp to 6.15pp
Absolute lift   = +3.85 percentage points
Relative lift   = +120.9%
```

*(Calculation reference: pooled-proportion z-test, standard formula
`z = (p2 - p1) / sqrt(p_pool*(1-p_pool)*(1/n1 + 1/n2))`. Replicable in Excel with
`NORM.S.DIST` on the z-statistic above, or in Python with `scipy.stats.norm`.)*

### Decision Rule

Reject H0 if one-tailed p-value < 0.05.

### Result

p-value (0.00055) << 0.05 → **Reject H0.**

### Business Interpretation

The new onboarding/activation campaign produces a statistically significant increase in
paid conversion rate — conversion more than doubles, from 3.19% to 7.04%. The 95%
confidence interval (1.56pp to 6.15pp) does not include zero, reinforcing that this is
very unlikely to be due to random variation given the sample size. This clears the bar
for the North Star evidence needed to support a launch recommendation — but per Task 8,
the launch decision still depends on whether guardrail metrics undercut this gain.

## Supporting Significance Checks (referenced in Task 8 guardrail analysis)

These aren't required by Task 7 (which only asks for a test on the primary metric), but
they're included here since they directly inform the guardrail evaluation and
recommendation memo.

| Metric | Control | Treatment | Test | Statistic | p-value | Read |
|---|---|---|---|---|---|---|
| Support ticket rate | 14.78% | 24.79% | Two-proportion z-test | z = 4.69 | < 0.0001 | Significant increase — real risk |
| Days to convert (converted users) | 8.86 days | 6.40 days | Welch's t-test | t = -2.80 | 0.008 | Significantly faster — positive signal |
| Engagement score | 57.03 | 62.94 | Welch's t-test | t = 7.93 | < 0.0001 | Significantly higher — positive signal |
| Refund rate | 0.00% (0/690) | 0.42% (3/710) | Not tested | — | — | Too few events (zero in one cell) for a reliable z-test; monitor as volume grows |
