 # Part 2- KPI Framework, Business Experiment Analysis & Decision Recommendation

## 1. Business Context

A subscription-based digital product company launched a new onboarding and activation
campaign aimed at improving user conversion and early engagement. Users were randomly
split into two groups:

- **Control** — existing onboarding experience
- **Treatment** — new campaign experience

Leadership needs a decision on whether to launch the new experience to all users.

### Business Problem Statement

1. **Decision to be made:** Whether to launch the new onboarding/activation campaign to
   100% of users, reject it, launch it only for specific segments, or continue testing
   before deciding.
2. **Who the decision impacts:** All new signups going through onboarding (product/growth
   team owns the rollout), the support team (who absorb any change in ticket volume), and
   finance (who own revenue and refund exposure).
3. **What metric should improve:** Paid conversion rate — the percentage of signed-up
   users who become paying customers (selected as the North Star; see Section 3).
4. **What risks must be monitored:** Refund rate, support ticket rate, revenue quality
   (average revenue per converted user), days to convert, and whether any user segment
   sees a decline instead of a lift.
5. **What evidence is required before a recommendation:** A statistically significant
   improvement in the North Star metric (paid conversion rate) between Treatment and
   Control, with guardrail metrics not showing a worsening trend large enough to offset
   the gain, checked at the overall level and at the segment level (region, device,
   plan type).

## 2. Dataset Description

- **File:** `campaign_experiment_data.xlsx`, sheet `experiment_data`
- **Raw rows:** 1,408 users, signed up between **2025-01-01 and 2025-05-31**
- **Clean rows used for analysis:** 1,400 users (8 exact duplicate user records removed
  — see Task 4 data-quality notes below)
- **Final group sizes:** Control = 690 users, Treatment = 710 users
- **Columns:** signup date, experiment group, region, device type, traffic source, plan
  type, and the full funnel — landing page visit, trial start, onboarding completion,
  paid conversion, revenue (30 days), support tickets (30 days), refund requested, days
  to convert, engagement score.


## 3. North Star Metric Selected

**Paid conversion rate** (`converted_to_paid` / total users in group).

- **Why this is the main success metric:** It is the only metric in the funnel that
  directly reflects whether a signup becomes paying revenue — the company's actual
  business objective. Every earlier funnel step (landing page visit, trial start,
  onboarding completion) is a leading indicator, not the outcome leadership is paying
  for.
- **Why other metrics are supporting, not North Star:** Landing page visit rate, trial
  start rate, onboarding completion rate, and engagement score all measure *progress*
  toward conversion, but a campaign can inflate any of these (e.g. a flashier landing
  page raises visit rate) without ever turning into revenue. They are diagnostic, not
  decision-grade.
- **How it connects to business growth:** Paid conversion rate, multiplied by user
  volume and average revenue per converted user, is the direct driver of MRR. It is the
  metric leadership can translate into a launch/no-launch revenue forecast.
- **What could go wrong if optimized blindly:** A team could juice paid conversion with
  aggressive upsell prompts or by converting low-intent users, which would show up as
  rising refunds, rising support load, or falling revenue-per-converted-user — exactly
  why guardrails (Section 8) are tracked alongside it rather than ignored.

## 4. KPI Tree Summary

*(Built in Task 3 — `outputs/kpi_tree.png`. The structure below mirrors that tree;
please adjust this summary if your diagram is labeled differently.)*

- **North Star:** Paid Conversion Rate
  - **Driver 1 — Top-of-funnel reach**
    - Landing page visit rate
    - Trial start rate
  - **Driver 2 — Activation quality**
    - Onboarding completion rate
    - Engagement score
  - **Driver 3 — Revenue realization**
    - Average revenue per converted user
    - Days to convert
- **Guardrails:** Refund rate, Support ticket rate, Segment-level conversion (region /
  device type / plan type)

## 5. Experiment Analysis Approach

1. Removed 8 exact duplicate `user_id` rows (verified the duplicate rows were identical
   across every column, not just the ID — safe to drop, not a merge conflict).
2. Confirmed all binary funnel columns (`visited_landing_page`, `started_trial`,
   `completed_onboarding`, `converted_to_paid`, `refund_requested`) only contain 0/1, and
   that the funnel is logically consistent (no user is marked converted without first
   being marked as starting a trial and completing onboarding, etc.).
3. Checked group balance: Control and Treatment are well-matched across region, device
   type, traffic source, and plan type (largest imbalance under 4 percentage points),
   so the random split looks healthy and segment-level results aren't confounded by an
   unbalanced split.
4. Flagged 4 high-revenue outliers among converted users (see Section 6) rather than
   deleting them — they are plausible large purchases, not data errors, but they
   distort the *mean* revenue-per-converted-user, so the median is reported alongside.
5. Ran a two-proportion z-test on the North Star metric (Section 7), plus supporting
   significance tests on the guardrail metrics most likely to reveal hidden cost
   (support ticket rate, days to convert, engagement score).
6. Cut the North Star metric by region, device type, and plan type to check for
   segments where the campaign underperforms (Section 9).

### Data Quality Notes (Task 4)

| Check | Finding | Handling |
|---|---|---|
| Missing values | `device_type` (18 rows), `traffic_source` (24 rows), `engagement_score` (14 rows) missing | Left blank; excluded only from the specific metric/segment cut they affect (not imputed, to avoid introducing bias) |
| Group counts | Control 690, Treatment 710 (after dedup) | Confirms a near-50/50 random split |
| Duplicate user IDs | 8 user IDs appeared twice, with fully identical rows | Dropped the duplicate occurrence, kept the first |
| Invalid binary values | None found — all binary columns are clean 0/1, funnel logic fully consistent | No action needed |
| Outliers in revenue | 4 converted users with revenue 2.5x+ the IQR upper bound ($8,610.72, $6,788.95, $3,887.98 in Control; $2,660.21 in Treatment) | Kept (real values), flagged, median reported alongside mean |
| Segment distribution across groups | Balanced within ~4pp across region/device/traffic/plan | No rebalancing needed |
| `days_to_convert` nulls | Null for ~1,328 of 1,400 rows | Expected — only populated for users who converted, not a data quality issue |

## 6. Experiment Result Summary

*(Full table in `outputs/experiment_summary.xlsx` — Task 5, completed by Chinmay.
High-level numbers below for traceability.)*

| Metric | Control (n=690) | Treatment (n=710) | Lift |
|---|---|---|---|
| Landing page visit rate | 63.62% | 72.39% | +8.8pp |
| Trial start rate | 25.07% | 29.01% | +3.9pp |
| Onboarding completion rate | 15.65% | 21.13% | +5.5pp |
| **Paid conversion rate (North Star)** | **3.19%** | **7.04%** | **+3.85pp (+121% relative)** |
| Avg revenue per user | $51.97 | $54.25 | +$2.28 |
| Avg revenue per converted user (mean) | $1,630.10 | $770.41 | -$859.69 |
| Avg revenue per converted user (median) | $813.14 | $452.48 | -$360.66 |
| Refund rate | 0.00% (0/690) | 0.42% (3/710) | +0.42pp |
| Support ticket rate | 14.78% | 24.79% | +10.0pp |
| Avg engagement score | 57.03 | 62.94 | +5.9 |
| Avg days to convert (converted users) | 8.86 days | 6.40 days | -2.5 days (faster) |

## 7. Hypothesis Test Summary

Full detail in `analysis/hypothesis_test_notes.md`. Headline result: a two-proportion
z-test on paid conversion rate gives **z = 3.26, one-tailed p ≈ 0.0005**, well under the
0.05 significance threshold — the lift is statistically significant, not noise.

## 8. Guardrail Metrics Considered

- **Refund rate:** 0% → 0.42% (3 events). Directionally up but too few events for a
  reliable significance test; worth monitoring as volume grows, not a launch blocker.
- **Support ticket rate:** 14.78% → 24.79%, **statistically significant increase**
  (z = 4.69, p < 0.0001). This is the most real guardrail risk — it implies higher
  ongoing support cost per cohort.
- **Revenue per converted user:** down on both mean and median — Treatment converts
  more users, but newly-converted users are lower-value on average. Net revenue per
  user (across all users, converted or not) is still slightly positive, so this isn't a
  net-revenue red flag, but it does change the customer mix.
- **Days to convert:** 8.86 → 6.40 days, significantly faster (p = 0.008) — a positive
  signal, not a risk.
- **Engagement score:** 57.03 → 62.94, significantly higher (p < 0.0001) — a positive
  signal.
- **Segment-level decline:** No segment shows a decline; Basic-plan users show almost no
  lift (3.62% → 3.88%), which is the one segment worth a closer look (Section 9).

## 9. Final Recommendation

**Launch** the new onboarding/activation campaign to all users, while actively
monitoring support ticket volume and keeping a watch on the Basic-plan segment, which
showed almost no lift. Full reasoning in `outputs/recommendation_memo.md`.

## 10. Assumptions and Limitations

- Assumed the 8 duplicate rows were a logging/export artifact, not a sign of a broader
  data pipeline issue — worth a quick check with whoever owns the data pipeline.
- Assumed the 4 high-revenue outliers are genuine large purchases (e.g. annual plans or
  upsells) rather than data entry errors, since there's no evidence in the data to flag
  them as errors.
- The conversion sample size is still small in absolute terms (22 Control conversions,
  50 Treatment conversions), so segment-level cuts (Section 9) are directionally useful
  but each individual segment cell has limited statistical power on its own.
- Revenue and refund figures are 30-day windows; longer-term retention, churn, and
  lifetime value effects of the campaign are not observable in this dataset.

## 11. Screenshots Included

This deliverable does not include the screenshot files (`summary_metrics.png`,
`hypothesis_test_output.png`, `kpi_tree_preview.png`) — per Chinmay's instruction,
these are being captured and added separately.
