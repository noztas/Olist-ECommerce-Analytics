# A/B Test Scorecard — Email Coupon v1

**Hypothesis:** 10% off coupon email lifts 30-day repurchase rate by ≥ 1pp
**Sample:** 12,010 customers, 50/50 split
**Duration:** 30 days
**Eligibility:** One-time buyers, 60–180 days since last order, as of 2018-09-01

## Primary metric — 30-day repurchase rate

| Group | Rate | n |
|---|---|---|
| Control | 3.58% | 6,005 |
| Treatment | 4.63% | 6,005 |

- **Absolute lift:** +1.05 pp
- **Relative lift:** +29.30%
- **95% CI on lift:** [+0.34 pp, +1.76 pp]
- **p-value:** 0.0038
- Statistical significance: YES
- Practical significance: YES

## Guardrails

| Guardrail | Treatment | Control | Change | Verdict |
|---|---|---|---|---|
| AOV (R$) | 151.42 | 149.32 | +1.41% | OK |
| Review score | 4.16 | 4.15 | — | OK |

## Decision: **SHIP**

Significant lift, above practical threshold, no guardrail breach.

### Post-launch monitoring
- **Cannibalization risk** — some of the +1pp may pull demand forward rather than create incremental purchases. Recommend a 90-day post-launch cohort comparison vs a permanent holdout to estimate true incrementality.
- **AOV drift** — our test showed AOV roughly stable, but the AOV sample was small (n≈250 per group). Monitor weekly post-launch.
- **Audience fatigue** — limit re-sends to one coupon per customer per quarter.

### Next experiments
1. **Coupon-depth test** — 10% vs 15% vs 20%. Find the discount level that maximizes incremental margin, not just incremental revenue.
2. **Send-time test** — same coupon, different time of day. Cheap to run, often yields meaningful lift.
3. **Holdout discipline** — keep a permanent ~5% holdout from all CRM sends to estimate long-term program ROI.

---

*Test design: α=0.05, power=0.80, MDE=1pp, two-sided. Random seed: 42. Methodology validated against simulated true effect of +1pp (recovered as +1.05pp, well within 95% CI).*