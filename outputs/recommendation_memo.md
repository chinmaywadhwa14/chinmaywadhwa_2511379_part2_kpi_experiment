# Recommendation Memo: Onboarding & Activation Campaign

## 1. Executive Summary

The new onboarding and activation campaign (Treatment) more than doubled paid
conversion rate versus the existing experience (Control) — 3.19% to 7.04%, a
statistically significant +3.85 percentage point lift (p ≈ 0.0005). Every upstream
funnel step also improved, and users converted faster and engaged more. The one real
guardrail concern is a significant rise in support ticket rate (14.78% → 24.79%).
**Recommendation: Launch to all users**, with operational monitoring on support load and
a closer look at the Basic-plan segment, which saw almost no benefit.

## 2. North Star Metric

**Paid conversion rate.** Selected because it is the only metric in the funnel that
reflects actual revenue-generating outcomes rather than progress toward them — landing
page visits, trial starts, and onboarding completions are all leading indicators that
can be inflated without producing paying customers. Full reasoning in README Section 3.

## 3. KPI Tree Explanation

The North Star (paid conversion rate) breaks down into three drivers: top-of-funnel
reach (landing page visits, trial starts), activation quality (onboarding completion,
engagement score), and revenue realization (revenue per converted user, days to
convert). Guardrails sit alongside the tree rather than inside it — refund rate, support
ticket rate, and segment-level conversion — because they catch cases where the North
Star improves for the wrong reasons (e.g. converting low-intent users who later refund
or need heavy support). Full diagram: `outputs/kpi_tree.png`.

## 4. Experiment Result Summary

| Metric | Control (n=690) | Treatment (n=710) | Lift |
|---|---|---|---|
| Landing page visit rate | 63.62% | 72.39% | +8.8pp |
| Trial start rate | 25.07% | 29.01% | +3.9pp |
| Onboarding completion rate | 15.65% | 21.13% | +5.5pp |
| **Paid conversion rate** | **3.19%** | **7.04%** | **+3.85pp (+121%)** |
| Avg revenue per user | $51.97 | $54.25 | +$2.28 |
| Avg revenue per converted user (median) | $813.14 | $452.48 | -$360.66 |
| Refund rate | 0.00% | 0.42% | +0.42pp |
| Support ticket rate | 14.78% | 24.79% | +10.0pp |
| Avg engagement score | 57.03 | 62.94 | +5.9 |
| Avg days to convert | 8.86 days | 6.40 days | -2.5 days |

Every step of the funnel moved in the right direction. The only metrics that moved
unfavorably are revenue-per-converted-user (down) and the two guardrails (refund rate,
support ticket rate), addressed below.

## 5. Hypothesis Test Interpretation

A one-tailed two-proportion z-test on paid conversion rate returned **z = 3.26,
p ≈ 0.0005**, well below the 0.05 significance threshold. We reject the null hypothesis
that the campaign has no effect. The 95% confidence interval for the lift (1.56pp to
6.15pp) excludes zero, and the relative lift (+121%) is large enough to matter
commercially, not just statistically. Full workings: `analysis/hypothesis_test_notes.md`.

## 6. Guardrail Analysis

| Guardrail | Finding | Risk level |
|---|---|---|
| Refund rate | 0% → 0.42% (3 events) | Low — directionally up but too few events to test reliably; not a launch blocker on its own |
| Support ticket rate | 14.78% → 24.79%, statistically significant (z=4.69, p<0.0001) | **Real risk** — implies higher ongoing support cost per cohort and should be budgeted for |
| Revenue per converted user | Down on mean and median; net revenue per user (all users) still up | Moderate — Treatment brings in more, lower-value converts, but the net is still revenue-positive |
| Days to convert | 8.86 → 6.40 days, significantly faster (p=0.008) | None — favorable |
| Engagement score | 57.03 → 62.94, significantly higher (p<0.0001) | None — favorable |
| Segment-level decline | No segment declined; Basic plan barely moved | Low-moderate — see Section 7 |

**Does any guardrail metric create risk?** Yes — support ticket rate is the one
guardrail with strong statistical evidence of a real, negative-direction shift. This
should be treated as an operating cost of the launch (likely needing additional support
headcount or self-serve deflection), not as a reason to withhold the launch, since the
conversion gain substantially outweighs it in expected revenue terms.

## 7. Segment-Level Insight

Paid conversion rate lift, Treatment vs Control, cut three ways:

**By region:** East 2.55%→6.47%, North 3.48%→8.89%, South 3.26%→7.65%,
West 3.38%→5.08%. Lift is positive everywhere; West is the weakest but still improves.

**By device type:** Desktop 4.50%→6.57%, Mobile 2.58%→7.41%, Tablet 1.82%→7.14%. Mobile
and Tablet — the majority of traffic — see the largest relative lift.

**By plan type:** Basic 3.62%→3.88% (essentially flat), Free 3.06%→9.29% (largest lift),
Premium 2.75%→6.25% (strong lift). **Basic-plan users are the one segment where the
campaign isn't clearly working** — worth investigating before or shortly after a full
launch (e.g. is the new onboarding flow less relevant to what Basic-plan users are
trying to do?).

## 8. Final Recommendation

**Launch.**

The North Star lift is large, statistically significant, and consistent across nearly
every region, device, and plan segment, with no segment showing a decline. The one
guardrail with real statistical signal (support ticket rate) represents a manageable
operating cost relative to a 121% relative lift in paid conversions, not a reason to
delay. The Basic-plan segment is the one open question — recommend launching broadly
now while specifically tracking Basic-plan conversion post-launch, rather than holding
the entire rollout for one segment.

## 9. Risks and Limitations

- Conversion counts are still small in absolute terms (22 Control, 50 Treatment
  conversions), so segment-level cuts, while directionally consistent, have less
  statistical power individually than the overall test.
- Refund rate's small increase (3 events) is not statistically testable yet — if refund
  volume continues climbing post-launch, it should be re-evaluated with more data.
- Revenue-per-converted-user is lower in Treatment; this is a customer-mix shift, not
  necessarily a problem, but finance should be aware the new cohort skews toward
  lower-value converts.
- Support ticket rate increase implies a real cost that isn't quantified in this dataset
  (no cost-per-ticket figure available) — recommend pricing this out before/alongside
  launch.
- This analysis is based on 30-day post-signup windows; longer-term retention and
  lifetime value effects aren't observable here.

## 10. Next Steps

1. Launch the new onboarding/activation experience to 100% of new signups.
2. Size up support team capacity ahead of launch given the ~10pp jump in ticket rate.
3. Track Basic-plan conversion specifically for 4-6 weeks post-launch; if it stays flat,
   investigate a Basic-plan-specific onboarding variant.
4. Re-run the refund-rate guardrail check once a larger post-launch sample is available.
5. Extend the analysis window beyond 30 days once data is available, to confirm the
   revenue and retention picture holds up longer-term.
