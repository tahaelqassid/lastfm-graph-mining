# Part E: Insights (LastFM)

## Q12. Answer to the main question (Q1)
Question: Do listening-taste communities exist in the LastFM network, and can bridge artists between
them be used to recommend across communities at a small cost in relevance?

Evidence:
- Communities exist: Louvain finds 7 large communities (83% of the connected nodes) with modularity 0.52
  (LPA only 0.36). They are readable genres (indie/alt rock, pop-punk/metal, mainstream pop, 80s synth-pop,
  classic rock/metal, experimental electronic, hip-hop) and the tags agree with the artists.
- But they are leaky: 18% to 44% of each community's listening goes outside it, and C1 (indie/alt rock)
  is the gateway that every other community feeds (6% to 23% of their listens).
- Bridges: swapping 3 of 10 slots for bridge artists from other communities cost no measurable Hit@10
  (-0.25 points, 95% interval -1.5 to +1.0), a small recall loss (-0.0075, about -5%), and gave +0.55
  communities per list (+22%), compared with plain PPR; PPR itself beats popularity (Hit@10 0.625 vs 0.418).
Answer: Yes. Taste communities are real, and bridge artists let the recommender cross them at a small,
measurable cost (about 5% of recall for 22% more communities at 3 slots).
Limitation: offline hold-out on 400 users and one split: it measures guessing hidden listens, not whether
users enjoy the surprise.

## Insight 1: Is listening concentrated on a few artists?
- Evidence: 60.6% of artists have exactly 1 listener; the top 1% (176 artists) hold 31.6% of listens
  and the top 10% hold 70.0%; the top artist (Lady Gaga) is in 32% of users' top 50.
- Answer: Yes, strongly heavy-tailed; a popularity recommender would repeat the same mainstream names.
- Limitation: each user only provides their top 50 artists, so the tail is censored.

## Insight 2: Is it a small world with social clustering?
- Evidence: average shortest path 4.56 on 300 sampled sources (99.7% of pairs within 6 steps) in a
  graph of about 20,000 nodes; the friend graph has 19,690 triangles vs 405 expected at random (48.6x)
  and average clustering 0.19 vs 0.007.
- Answer: Yes: everything is a few steps away (97.96% in one component) and friends form tight circles.
- Limitation: distances are sampled (diameter 10 is a lower bound) and the random baseline ignores hubs,
  so 48.6x overstates the social effect.

## Insight 3: Which communities are open, and which stay home?
- Evidence: pop (C3) keeps 82.4% of its listening inside, hip-hop (C7) only 55.8%; indie/alt rock (C1)
  receives 14% to 23% of the listens of C2, C4, C5, C6 and C7.
- Answer: Pop is a destination, not a source (others visit it, its users rarely leave); indie/alt rock is
  the central gateway. Bridge artists such as ABBA, Queen, Metallica or Ramones carry the crossings.
- Limitation: one Louvain run (stability NMI about 0.66), so exact percentages move slightly between runs.

## Insight 4: Does graph structure beat popularity, and where does it fail?
- Evidence: PPR Hit@10 0.625 vs 0.418 for popularity (95% interval of the gap +0.17 to +0.25);
  but for the isolated user (2 listens of 1-listener artists) all PPR scores were 0, and for a niche but
  connected user PPR drifted to hubs (Kanye West, Lady Gaga, Coldplay...).
- Answer: Structure clearly helps on average, but it needs a popularity fallback for isolated users and
  suffers popularity bias for niche tastes.
- Limitation: cold start: 12.5% of hidden listens involve artists that disappear from the training graph, so
  no method can find them, and PPR is slow (about 2 s per user).