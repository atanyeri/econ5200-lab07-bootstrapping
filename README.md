# Diagnosing Broken Confidence Intervals

## Objective
I tested three situations where bootstrap and standard confidence intervals fail without an obvious warning, and checked that the repairs work.

## Methodology
- Ran a percentile bootstrap on the median of 32,561 integer ages and examined why the resamples did not vary. Then asked SciPy for a BCa interval on the same statistic.
- Simulated skewed data (n = 50, 400 experiments) and measured how often nominal 95% intervals contained the true value. I compared parametric and bootstrap intervals for both the mean and the median.
- Built a panel of wages from 25 firms and bootstrapped it two ways: resampling workers as if independent, and resampling whole firms. I compared interval widths and coverage.
- Wrote a `bootstrap_utils.py` module with percentile, i.i.d. and cluster bootstrap functions.
- Had an AI write a coverage function, revised my prompt once to fix how it handled seeds, and checked its output against my own coverage numbers.

## Key Findings
- The bootstrap SE for the median age was 0. Ages are whole numbers with many ties, so every resample gave the same median. This does not mean the median is known exactly. BCa returned nan with a warning instead of an interval, which was the correct response to degenerate data.
- For the mean, parametric coverage was 92.5% and bootstrap coverage was 92.0%. The bootstrap did not fix the skew. For the median, parametric coverage was 66.8% and bootstrap coverage was 92.8%. The parametric interval is built for the mean, so it misses the median.
- Resampling workers instead of firms gave an interval 5.7x too narrow, with 19.5% coverage. Resampling whole firms brought coverage to 91.0%.
- The AI's coverage function matched my own numbers exactly (19.5% and 91.0%) once I specified that repetition number t is the seed passed to both functions. My first prompt left the seed unspecified, so the AI invented its own scheme, which could not reproduce my results.
