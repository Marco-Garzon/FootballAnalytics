# Striker defensive work, goal output and team position

## Research question

**After adjusting for time spent without the ball, is striker defensive involvement associated with goal output and final team position among central forwards with at least 1,200 minutes in the 2015/16 Premier League and La Liga?**

This is an exploratory association study, not a causal test.

## Results

### Premier League

- Cohort: **36 players**.
- Adjusted activity vs goals per 90: Spearman **ρ = -0.11**, p = 0.537.
- Adjusted activity vs final team position: Spearman **ρ = +0.07**, p = 0.676.
- The unadjusted per-90 goals relationship was **ρ = -0.17**.
- Player rankings across 10-, 15- and 20-second timing caps remained at least **ρ = 1.00**.

### La Liga

- Cohort: **33 players**.
- Adjusted activity vs goals per 90: Spearman **ρ = -0.16**, p = 0.377.
- Adjusted activity vs final team position: Spearman **ρ = +0.26**, p = 0.139.
- The unadjusted per-90 goals relationship was **ρ = -0.23**.
- Player rankings across 10-, 15- and 20-second timing caps remained at least **ρ = 1.00**.

## Metric definitions

- **Goals per 90:** StatsBomb shot events with outcome `Goal`, divided by estimated player minutes.
- **Defensive activity per 30 estimated opposition-possession minutes:** pressures, duels, recoveries, interceptions and blocks divided by reconstructed time without the ball, multiplied by 30.
- **Possession-adjusted pressing index:** standardized pressure and counterpressure rates using the same opposition-possession denominator.
- **Possession-adjusted intervention index:** adjusted recoveries, interceptions, duels, blocks and dribbled-past events, standardized within each league.

## Why this denominator

- Per 90 controls for playing time but not defensive opportunity. Strikers in possession-dominant teams spend less time defending.
- Per 100 opposition possessions treats short and long possessions as equal opportunities, despite very different defensive exposure.
- Opposition-possession minutes preserve duration. Scaling to 30 minutes gives every player the same estimated time without the ball.

## Timing reconstruction

StatsBomb supplies event timestamps, possession IDs and possession teams rather than a continuous possession stopwatch. Consecutive live-event intervals are assigned to the recorded possession team. Dead-ball intervals are excluded and gaps are capped at 15 seconds. Ten- and twenty-second caps test whether the rankings and conclusions depend on that timing choice.

## Interpretation guardrails

- Correlation is not causation.
- Spearman rank correlation is primary because team position is ordinal.
- Players from the same club are not independent observations.
- Field position, pressing scheme and opponent buildup style still affect opportunity after the time adjustment.
- The 1,200-minute and central-forward rules shape the cohort.

## Recommended next step

Add opponent buildup location or pass volume and fit a multivariable model. This would separate time spent defending from the type and location of defensive opportunities a striker receives.
