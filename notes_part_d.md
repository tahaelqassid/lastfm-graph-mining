# Part D2: Personalized PageRank recommender

## Setup
- Hold-out: 20% of each user's listens hidden (seed 42): 18,577 of 92,834 listens;
  every user has at least one hidden artist.
- Training graph: 17,326 nodes = 1,884 users + 15,442 artists, 74,257 listens
  (148,514 relationships, undirected). 8 users have no training edge and are not evaluated.
- Cold start: 2,190 artists with listens vanish from the training graph (all their listens were hidden);
  only 87.5% of hidden listens involve an artist still present, so Hit@10 has a ceiling.
  Evaluation counts only hidden artists that remain in training.
- Strategies compared: popularity baseline, PPR, community-aware PPR (to do).
- Metrics: Hit@10 (share of users with at least one hidden artist in their top 10),
  and diversity (number of distinct Louvain communities among the 10 recommendations).

  ## PPR demos (3 chosen users, damping 0.85)
- Niche user 1266 (2 training artists, 1 listener each): all PPR scores are 0: his component is
  disconnected, so the walk cannot leave it (cold start); a popularity fallback is needed.
- Mainstream user 797 (mean artist popularity 286): 10/10 recommendations in pop (C3), 1 hit
  (Britney Spears): accurate but no diversity (filter bubble).
- High-betweenness user 3 (36 of 40 training listens in C6, 4 in C1): 5/10 recommendations from C1
  (Sigur Ros, Bonobo, Radiohead, Helios, Coldplay), 1 hit (Deru). PPR crosses communities along the
  C6 to C1 gateway; his hidden artists are very obscure, and PPR favors well-connected artists.

  ## Users without training edges
- 8 users (ids 112, 615, 1013, 1307, 1603, 1731, 1758, 2085) have a single listen in the whole dataset,
  which was hidden: not evaluable. Evaluation uses the remaining 1,884 users.

## Niche but connected user (116: 10th percentile of artist popularity, 40 training listens)
- PPR recommends mainstream artists (Kanye West, 2Pac, Lady Gaga, Coldplay, Britney Spears...), 0 hits:
  PPR is attracted to hubs, so popularity bias reappears for niche listeners.

## Evaluation (400 random users, seed 42, 10 recommendations each, communities from training graph only)
| Strategy | Hit@10 | Recall@10 | Avg communities in top 10 | Share outside home |
| Popularity | 0.418 | 0.077 | 2.09 | 0.815 |
| PPR | 0.625 | 0.139 | 2.43 | 0.568 |
| Community-aware PPR (3 slots) | 0.622 | 0.132 | 2.98 | 0.654 |
- PPR beats popularity: Hit@10 +20.7 points (about 1.5x), recall about 1.8x.
- Community-aware PPR: no measurable loss in Hit@10 (0.3 point, within noise), recall -5% relative,
  +22% communities covered and +8.6 points of recommendations outside the home community.
- "Outside home" must be read with accuracy: popularity scores high only because it recommends
  the same mainstream artists to everyone.
- Rule: 7 best PPR artists + up to 3 bridge artists (>= 10 listeners, >= 40% outside listeners) from a
  community other than the user's own, taken from the PPR top 60; if fewer, filled by next-best PPR.
- Leakage control: communities and bridge scores recomputed on the training graph only (24 communities,
  540 bridge artists).
- Limitations: 400 users (Hit@10 margin about +-4.7 points); bridges only from PPR top 60; training-graph
  communities include small ones; hold-out accuracy is not user satisfaction; one split, one Louvain run.

  - Paired bootstrap, 95% intervals (400 users, 2,000 resamples):
  Hit@10 aware - PPR: -0.0025 [-0.015, +0.010] (no measurable loss);
  Recall@10 aware - PPR: -0.0075 [-0.0113, -0.0037] (small but real loss, about -5% relative);
  Communities aware - PPR: +0.55 [+0.47, +0.63] (real gain, +22%);
  Hit@10 PPR - popularity: +0.2075 [+0.1675, +0.2475]; Recall@10 +0.062 [+0.0535, +0.0706].

## Trade-off by number of bridge slots (0 = plain PPR)
| Slots | Hit@10 | Recall@10 | Communities | Outside home |
| 0 | 0.625 | 0.139 | 2.43 | 0.568 |
| 1 | 0.625 | 0.138 | 2.64 | 0.587 |
| 2 | 0.622 | 0.136 | 2.81 | 0.616 |
| 3 | 0.622 | 0.132 | 2.98 | 0.654 |
| 4 | 0.632 | 0.128 | 3.22 | 0.696 |
| 5 | 0.632 | 0.124 | 3.42 | 0.742 |
- Recall falls about 0.003 per extra slot (-11% at 5 slots), Hit@10 stays flat (differences within noise),
  communities rise about 0.2 per slot (+41% at 5 slots): about 0.015 recall per extra community.
- Suggested setting: 2 to 3 slots (keeps 95-98% of recall, +0.38 to +0.55 communities); beyond that it is a product choice.
- Cost: 783 s for 400 PPR runs (about 2 s each): per-request PPR does not scale; precompute in practice.