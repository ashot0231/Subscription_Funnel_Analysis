# Subscription Funnel Analysis

> **Analyze the end-to-end subscription funnel of a mobile creative editor across 4 markets and 2 platforms. Identify where users drop off, what drives conversion, and recommend prioritized product actions.**

---
Data link https://drive.google.com/drive/folders/1hdjv8YJCPiEDW3GpsdLVFDAuGzHivHvj?usp=share_link

---

## Overview

| Detail | Value |
|---|---|
| Observation window | November 6–19, 2023 (14 days) |
| Markets | Brazil (BR), Vietnam (VN), Egypt (EG), Germany (DE) |
| Platforms | Android, Apple |
| Unit of analysis | Device (`device_skey`) |
| Total unique devices | ~39,000 |
| End-to-end subscribers | ~99 (~0.25% conversion) |

---

## Project Structure

```
funnel_analysis.ipynb     ← Main analysis notebook (all steps executed)
recommendations.md        ← Standalone findings & recommendations write-up
```

---

## Notebook Steps

| Step | Description |
|---|---|
| Step 0 | Setup, data loading, column inventory |
| Step 1 | Funnel construction — device-level event sequencing |
| Step 2 | Top-of-funnel analysis (`app_open` → `registration_open`) |
| Step 3 | Registration deep dive (completion rate, platform gap, country breakdown) |
| Step 4 | Paywall analysis — source breakdown, conversion by trigger type |
| Step 5 | Session depth analysis — pre-paywall activity vs. conversion rate |
| Step 6 | New-install vs. returning-user cohort comparison |
| Step 7 | **Findings & Recommendations** (write-up) |
| Step 8 | Bonus: A/B test design + sample size calculation |

---

## Key Findings

- **Top-of-funnel is the dominant bottleneck.** Over 90% of devices that open the app never reach the registration screen. Fixing this step has greater absolute impact than optimizing any downstream step.
- **Platform gap is real.** Apple devices consistently outperform Android on end-to-end conversion, most likely due to one-tap Sign in with Apple reducing registration friction.
- **Paywall source matters more than volume.** The highest-converting paywall triggers are those where the user hit a hard feature gate during active editing — not opportunistic mid-session interruptions.
- **Session depth predicts conversion.** Users who see the paywall after zero prior editing actions convert at the lowest rate. Conversion climbs with pre-paywall session depth and plateaus at 3–10 prior events.
- **Country-level conversion is concentrated.** One or two markets account for a disproportionate share of subscriptions relative to traffic, driven by local payment method availability and price sensitivity.

---

## Recommendations

1. **Session-depth gate on the paywall** — suppress `subscription_offer_open` until ≥ 3 in-session editing actions have occurred. Validate with a 50/50 A/B test.
2. **Reroute paywall surfacing** toward high-converting intent-based trigger sources; replace low-converting opportunistic triggers with soft feature teasers.
3. **Android registration UX + local payment methods** — streamline Android sign-up to reduce friction toward Apple Sign In parity; integrate locally dominant payment methods in VN and EG.

---

## Bonus A/B Test Design

**Hypothesis:** suppressing the paywall until session depth ≥ 3 will increase the paywall-to-subscription conversion rate by ≥ 30% relative.

- Unit of randomization: `(device_skey, session_skey)`
- Primary metric: `subscription_done / subscription_offer_open` sessions
- Secondary (guardrail): `subscription_done / app_open` (to detect volume dilution)
- Alpha: 0.05 (two-sided), Power: 0.80, MDE: +30% relative lift
- Sample size: see Step 8a sensitivity table

---

## Limitations

- 14-day window — cannot evaluate retention, trial-to-paid conversion, or seasonality.
- ~99 total subscribers — segment-level rates are noisy; not all gaps survive Fisher's exact test at this sample size.
- No revenue or LTV data — conversion rate is the only outcome metric available.
- Device-level identity — cross-device journeys are invisible; factory resets create new identities.
- Event fidelity gaps — `registration_done`-without-`registration_open` cases (~X%) indicate an imperfect event pipeline.

---

## How to Run

```bash
# Install dependencies
pip install pandas numpy plotly scipy statsmodels

# Launch the notebook
jupyter notebook funnel_analysis.ipynb
```

All cells are pre-executed and outputs are visible. Re-run from top to bottom for full reproducibility. The notebook reads from `Growth Task Data` folder in the working directory — update `DATA_DIR` in Step 0 if your file path differs.
