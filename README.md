# Paywall A/B Test

Analysis of an A/B test on a redesigned paywall for FormWise, a made-up online legal-document subscription service.
The data is simulated. `data/generate_data.py` builds it, and the script is seeded so the numbers are reproducible.

The new paywall shows users a preview of the document they just filled in, plus a clearer comparison of the
monthly and annual plans. The question was whether it gets more people to subscribe.

## Approach
- Sized the test up front with a power analysis (baseline ~19%, minimum detectable effect +15%, alpha 0.05, 80%
  power). That came out to about 10 weeks.
- Checked for sample ratio mismatch and confirmed both groups reached the paywall at the same rate.
- Counted purchases from billing data, because the front-end purchase event was missing user IDs for part of the
  test period.
- Two-proportion z-test, Newcombe CI for the difference, and a bootstrap CI for relative lift.
- Device breakdown with a Holm correction, treated as exploratory.

## Results
| | Control | Treatment |
|---|---|---|
| Paywall viewers | 3,538 | 3,457 |
| Conversion | 19.6% | 20.9% |

- Overall lift of +6.9% relative, 95% CI -2.6% to +17.4%, p = 0.16. **Not significant.** The real effect is
  probably smaller than the test was sized to detect.
- Mobile went from 13.1% to 17.0% (+30%), still significant after the Holm adjustment (p = 0.043). Desktop
  didn't change.
- Revenue per viewer and annual plan share both moved up, but neither was significant.

![Lift by device](figures/lift_by_device.png)

## Recommendation
Don't report an overall win. For mobile it's a trade-off: a properly sized mobile-only test would take about 28
weeks, or the new paywall could ship to mobile with a 10% holdout to keep measuring. Keep the current paywall on
desktop.

In hindsight, annual plan share was the wrong guardrail. My retention analysis on the same data shows monthly
subscribers are worth more in year one, so next time I'd use expected 12-month revenue per viewer.

## Run it
```
pip install -r requirements.txt
python data/generate_data.py
jupyter notebook analysis.ipynb
```

Tools: Python, pandas, SciPy, statsmodels, Matplotlib, SQLite
