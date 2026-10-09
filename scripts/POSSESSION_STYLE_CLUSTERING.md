# Possession-style classification methodology

This guide documents how attacking possession styles are reconstructed and
classified in the 2017/18 Wyscout Big Five analysis.

## What counts as one possession?

A possession is one uninterrupted sequence of team control. It starts when a
team performs a controlled on-ball action and ends when the opponent next
performs a controlled action, a shot occurs, play stops, a restart happens, or
a period ends. Opponent duel records alone do not trigger a change of
possession because Wyscout frequently records the same duel for both teams.

Only open-play sequences with at least three recorded actions and one pass are
eligible for clustering. By default, sequences beginning with set pieces are
excluded.

## Tactical inputs

Each eligible possession is represented by 18 event-derived variables:

- duration;
- number of actions;
- starting field position;
- net forward progression;
- forward speed;
- lateral spread / width;
- average pass length;
- proportion of actions that are passes;
- pass accuracy;
- forward-pass share;
- progressive-pass share;
- long-pass share;
- cross share;
- smart-pass share;
- attacking-duel share;
- aerial-duel share;
- final-third entries per action; and
- box entries per action.

Shots and goals are excluded from the clustering inputs and are used later to
profile outcomes for each discovered style.

## Preprocessing

The variables are prepared in a fixed order:

1. Missing values are replaced with the median for their feature.
2. Possession duration, action count, and mean pass length are transformed with
   `log(1 + x)`. This reduces the influence of unusually long possessions or
   exceptionally long passes.
3. Every feature is winsorised: values below its 1st percentile are set to that
   percentile, and values above its 99th percentile are set to that percentile.
4. Every processed feature is standardised:

   `z_ij = (x_tilde_ij - mu_j) / sigma_j`

Here, `x_tilde_ij` is the value after median-filling, any applicable log
transformation, and percentile capping. The medians, percentile caps, means,
and standard deviations are learned from a league-balanced fitting sample, then
applied unchanged to every eligible possession. This prevents a larger league
from setting the scale for all other leagues.

## Clustering

The clustering begins without tactical labels and finds recurring patterns
among possessions with similar processed event profiles. After clustering,
rule-based suggestions use each cluster's feature profile to help name it.
Those naming rules describe clusters; they do not assign individual
possessions to them.

PCA reduces the 18 standardised features to the smallest number of components
that retain at least 90% of their variation. Repeated K-means++ solutions from
`K=3` to `K=8` are compared using silhouette score, Davies-Bouldin score,
inertia, and minimum cluster share. In the reported run, 12 components were
retained, and the selected `K=6` model was applied to all eligible possessions.
K=6 had the highest sampled silhouette score (0.149) among the viable
solutions, with the smallest cluster representing 4.7% of the fitting sample.
The modest silhouette score indicates that the styles overlap rather than form
perfectly separated categories.

Each possession `i` receives the closest cluster centre:

`Style(i) = argmin_{k in {1, ..., 6}} ||p_i - c_k||^2`

where `p_i` is the PCA-reduced profile of possession `i`, and `c_k` is the
centre of cluster `k`. This is K-means clustering, not a classification tree
or a single-feature rule.

`raw possession events -> 18 tactical features -> processed and standardised features -> PCA representation -> nearest cluster`

## Interpreting the discovered types

The style names describe each cluster's characteristic profile. A single
possession is assigned by its overall similarity to a cluster, not by a rule
such as “three passes equals patient circulation” or “one long ball equals
direct progression”.

| Type | What it means in this analysis | Main signals in the data |
|---|---|---|
| Patient circulation | A longer, controlled attack that moves the ball across the pitch before progressing. | Long duration, many actions, high pass share and accuracy, wider movement, slower progression. |
| Recycling possession | A sequence in which the team retains the ball but makes limited net progress toward goal. | Shorter passes, low forward or net progression, limited final-third or box entry. |
| Fast transition | A quick attack that gains ground rapidly once the team has the ball. | Short duration, high forward speed, strong forward/progressive passing, final-third and box entries. |
| Direct progression | An attack that seeks territory quickly, often by moving the ball forward with longer or more vertical passes. | High long-pass and progressive-pass share, forward movement, relatively short sequence. |
| Wide crossing attack | An attack developed toward wide areas and finished or advanced through crosses. | High cross share, wide spread, long passing, box entries. |
| Line-breaking combination | A possession built around passes that break through defensive lines. | High share of Wyscout “smart passes”, forward penetration, entries into dangerous areas. |

## Limits of interpretation

Cluster names are interpretation aids rather than ground truth. The profiles
overlap, and the 3-action / 1-pass eligibility threshold excludes some very
short sequences. League and team comparisons should therefore use each side's
share of eligible possessions assigned to each style, rather than raw counts
alone. The labels describe patterns in this dataset; they do not establish
causation.
