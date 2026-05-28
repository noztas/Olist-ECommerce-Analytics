# A/B Test: Email Coupon for One-Time Buyers

**What I tested:** Does a 10% off coupon email actually bring one-time buyers back?

**Setup:** 12,010 customers from the silver layer, all one-time buyers who hadn't ordered in 60 to 180 days. Split 50/50: half got the email, half didn't. Measured who came back within 30 days.

**Why 12,010?** That's the sample size needed to reliably detect a 1 percentage point lift (α = 0.05, power = 0.80). Power analysis details are in the notebook.

## Results

### Did they come back?

| Group | Repurchase rate | Customers |
|---|---|---|
| No email (control) | 3.58% | 6,005 |
| Email (treatment) | 4.63% | 6,005 |

The email lifted repurchase rate by **1.05 percentage points** (95% CI: 0.34 to 1.76, p = 0.0038).

In plain language: about 1 extra customer per 100 came back because of the email. It's not huge, but it's real.

### Did it hurt anything?

| Guardrail | Control | Treatment | Verdict |
|---|---|---|---|
| Average order value | R$ 149.32 | R$ 151.42 | OK, no drop |
| Review score | 4.15 | 4.16 | OK, both above 4.0 |

Nothing broke. Margin held, reviews held.

## Decision: Ship it

The lift is real, it's above the minimum I care about (0.5 pp), and no guardrails broke. Send the coupon to the rest of the eligible base.

## What I'd watch after launch

A few things to keep an eye on before calling this a full win:

1. **Were these customers going to buy anyway?** Some of the lift might just be people who'd have come back on their own, just sooner. To find out, keep a small group permanently out of the campaign and compare them after 90 days.

2. **The AOV story is fragile.** Only about 250 customers actually repurchased in each group, so the AOV numbers have wide error bars. Keep watching it weekly once the campaign is live.

3. **Don't burn out the audience.** One coupon per customer per quarter, max.

## What I'd test next

1. **Different coupon amounts.** 10% vs 15% vs 20%. Find the level that maximizes profit per email, not just response rate.
2. **Send time of day.** Cheap to test, often surprising results.
3. **Build a permanent holdout group.** Keep 5% of customers out of every CRM campaign forever, so we can measure how much the whole program is actually worth.

---

*Test setup: α = 0.05, power = 0.80, MDE = 1pp, two-sided. Random seed: 42. The test was simulated end-to-end on real Olist customers with a baked-in true effect of +1 pp, and the analysis recovered +1.05 pp. So I trust the methodology.*