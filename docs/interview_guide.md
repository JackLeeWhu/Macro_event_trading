# Interview walkthrough

[Project overview](../README.md) · [Methodology](methodology.md) · [Results](results.md) · [中文提纲](README_CN.md)

## A 90-second explanation

My project studies whether an inflation announcement is still surprising after accounting for information visible before its release. I used Chinese CPI as the case study and evaluated the idea through a duration overlay on a long-dated government bond price index.

There are two information streams. First, I built a daily inflation proxy from high-frequency food prices, using smoothing and lagged inverse-volatility weights. Second, I measured realized CPI relative to the cleaned distribution of economists' forecasts. I then adjusted the survey surprise for the pre-release movement in the daily proxy.

The economic intuition is that an upside CPI surprise may contain less new information if inflation-sensitive prices have already risen. A relatively negative adjusted surprise is hypothesized to support duration. I translated that score into a temporary increase in bond exposure, combined it with a separate return-based risk filter, and lagged target exposure before calculating returns.

In the historical research sample from January 2018 to September 2026, the combined configuration returned about 13% annualized with a Sharpe of 2.18 and a maximum drawdown of 5.33%. The passive net-price index returned about 4.45% annualized. These are gross exploratory results, without an independently documented untouched holdout. My next priorities are to isolate the macro signal's incremental contribution, test stability chronologically, and model realistic execution and financing.

## A five-minute structure

| Time | Topic | What to show |
| --- | --- | --- |
| 0:00–0:45 | Economic question | Why survey surprise can differ from information that remains unanticipated |
| 0:45–1:45 | Measurement | The two information streams and the workflow diagram |
| 1:45–2:30 | Timing and allocation | Reference month versus release date; pre-release windows; lagged exposure |
| 2:30–3:30 | Evidence | Performance, drawdowns, annual results, and benchmark definition |
| 3:30–5:00 | Critique and next tests | Ablation, untouched holdout, score calibration, costs, and execution |

## Likely technical questions

### Why call this factor mimicking?

The index aggregates economically motivated inflation-related price changes into a daily proxy for a monthly economic variable. It uses lagged inverse-volatility weighting, rather than optimizing coefficients to replicate CPI. I would describe it as an economically constructed factor proxy and make that distinction explicit.

### Is the pre-release score really a measure of market expectations?

It is a hypothesis-driven proxy. Food-price data may represent timely information about inflation, but do not directly reveal investor positions, bond prices, or expectation quotes. A stronger validation would compare the score with independently measured expectations and examine whether it adds predictive information beyond the survey surprise.

### Why subtract the two scores?

Subtraction encodes the economic idea of removing information already reflected in the proxy. The numerical mapping is a modeling choice. A shared bounded range does not make two scores statistically comparable. I would examine the distributions, saturation, and coefficient stability using training-only calibration, then compare the simple difference with alternative combinations on a held-out sample.

### Why inverse-volatility weights?

They limit the extent to which a volatile food-price component mechanically dominates the daily aggregate. The weights are estimated from historical information and lagged. The tradeoff is that lower volatility does not imply greater economic importance for CPI. Equal weights, expenditure-based weights, and training-only fitted weights would be useful comparisons; they are not reported as completed tests here.

### What is the main look-ahead risk?

There are several. The realized CPI must be available before the strategy can use the complete score. The proxy windows must exclude the release day. Analyst submissions made on the release date need timestamps, and source food prices may have publication delays. A one-record exposure lag is an accounting safeguard, but does not prove that a release-day closing fill was obtainable.

### Can the Welch statistic be used for a p-value here?

Not with a naive independent-sample interpretation. Smoothing creates serial dependence, and adjacent windows can share underlying information. I use it as a relative intensity score. Formal inference should use an appropriate dependence-aware method, while the trading claim should be evaluated on subsequent returns.

### How much of the performance comes from the macro signal?

That is not isolated by the headline result. The configuration combines the macro overlay, variable leverage, and a return filter. The required ablation compares passive exposure, survey-only exposure, adjusted-surprise exposure, filter-only exposure, and the combined strategy under consistent risk and cost assumptions.

### Does average exposure near 1× make the benchmark fair?

It is a useful descriptive statistic, but is insufficient. Exposure timing, duration, volatility, and drawdown risk differ. A matched-volatility or matched-duration comparison and an active-return analysis would provide a stronger benchmark assessment.

### How would you validate the model out of sample?

Freeze the economic definitions and all parameter choices before evaluating a later chronological sample. Use a separate training/validation process for any calibration. Where event holding periods overlap, prevent the same realized-return window from contributing to both sides of a split. Report the distribution of results across reasonable neighboring parameters rather than selecting a single attractive setting.

### Why use a bond net-price index?

It gives a clear price-response benchmark for duration exposure. It excludes coupon income and is not directly tradable. A production study would map the exposure to an investable bond or futures portfolio, then account for carry, financing, duration changes, rolls, and execution costs.

### What would you improve first?

First, establish incremental value with ablations and risk-matched benchmarks. Second, audit point-in-time availability and rerun on a consistent trading calendar. Third, freeze the design for a chronological holdout and add executable cost assumptions. Those steps address attribution, timing, and investability before adding model complexity.

## Claims supported by this presentation

- A complete research workflow was built from economic hypothesis to event-based portfolio evaluation.
- The archived calculation aligns CPI reference months with release dates and uses strictly pre-release proxy windows.
- The displayed returns follow the stated lagged-exposure accounting rule.
- The published snapshot is a combined, gross, exploratory backtest on a net-price index.

Claims such as “live alpha,” “cost-adjusted performance,” “out-of-sample Sharpe 2.18,” “observed buyer expectations,” or “validated futures execution” are not supported by these materials.
