# Part B: Graph properties (LastFM)

Projection used: "listen" = User + Artist nodes, LISTEN_TO edges, undirected, weighted by plays.
Names: GDS artist rawId = artists.dat id + 10000 (verified: top hubs are the known top artists).

## Q4. Degree
- Users: 1,892, mean 49.07, median 50, max 50.
  Interpretation: degree is capped at 50 because the dataset only contains
  each user's top 50 artists; it reflects data collection, not behavior.
- Artists: 17,632 with listens (390 tag-only artists excluded),
  mean 5.27, median 1, max 611.
  Interpretation: popularity is heavy-tailed; the top artist is in the top 50
  of about 32% of users, while half of the artists have one listener.
- 60.6% of artists have exactly 1 listener.
  Interpretation: most of the catalogue is a long tail of niche artists.
- Top 1% of artists (176) hold 31.6% of all listen links; top 10% hold 70.0%.
  Interpretation: strong concentration on a few hubs.
- Top 10 artists by degree: Lady Gaga (611), Britney Spears (522), Rihanna (484),
  The Beatles (480), Katy Perry (473), Madonna (429), Avril Lavigne (417),
  Christina Aguilera (407), Muse (400), Paramore (399).
  Interpretation: hubs are mainstream pop names, so a popularity-based
  recommender would keep suggesting the same artists.
- Conclusion Q4: yes, the artist side is dominated by a few hubs.
  Limitation: user degree is capped at 50 by how the dataset was built.

## Q5. Components and distances
- Listening projection: 19,914 nodes, 398 components. 8 have more than one node;
  the other 390 are the tag-only artists (isolated in this projection).
  Largest component: 19,507 nodes = 97.96% of the projection
  (99.9% of the 19,524 nodes that have a listen edge). The 7 small components hold 17 nodes in total.
  Interpretation: the network is essentially one connected block, so
  a recommender can reach almost every artist from almost every user.
- Average shortest path (300 random sources, seed 42): 4.56 steps.
  About 99.7% of sampled pairs are within 6 steps.
  Diameter lower bound found: 10 (only 102 sampled pairs at distance 10).
  Interpretation: small world. Almost any two nodes are 4 to 6 steps apart
  in a graph of ~20,000 nodes, comparable to a random graph (ln n / ln k, about 4.4).
- Parity note: the listening graph is bipartite, so artist-to-artist distances are even
  and artist-to-user distances are odd; this explains the jagged distance histogram.
- Friend graph: 1,892 users (all have at least one friend), 12,717 edges,
  20 components, largest 1,843 users (97.4%).
  Interpretation: the social layer is also almost fully connected.
- Limitations: distances are estimated from a 300-node sample, so the diameter is
  a lower bound (the true value can be larger); the sample is random, with seed 42.


## Q6. Triangles and clustering (friendship graph)
- Why friendship graph: the listening graph is bipartite (user-artist only),
  so it has 0 triangles by construction.
- Friend graph: 1,892 users, 12,717 edges (mean 13.4 friends per user).
- Triangles: 19,690. Expected in a random graph with the same n and m
  (C(n,3) * p^3, p = 0.00711): 404.9. Ratio: 48.6x.
- Average clustering coefficient: 0.1865 vs 0.0071 in the random graph (about 26x higher).
- Interpretation: friends of a user tend to be friends with each other, so the
  social layer has tight circles; this supports looking for communities in Part C.
- Limitations: the random baseline ignores hubs, which already raise triangle
  counts, so 48.6x overstates the "social" effect; GDS gives 0 to users with fewer than 2 friends.
  
## Q7. Betweenness (listening projection, sampled: 2,000 sources, seed 42)
- Top 10 artists by betweenness: The Beatles, Lady Gaga, Radiohead, Muse, Depeche Mode,
  Madonna, Coldplay, Britney Spears, Paramore, Pink Floyd.
- Overlap with the top-10 by degree: 6 of 10 (Beatles, Lady Gaga, Muse, Madonna, Britney Spears, Paramore).
  Only in top-10 betweenness: Radiohead, Depeche Mode, Coldplay, Pink Floyd.
  Only in top-10 degree: Rihanna, Katy Perry, Avril Lavigne, Christina Aguilera.
- Example: Depeche Mode has fewer listeners (282) than Madonna (429) but higher betweenness.
- Interpretation: top-betweenness artists are crossroads shared by listeners of different tastes
  (hypothesis: rock/alternative acts bridge groups; pure pop hubs overlap with each other).
  To be tested with communities in Part C.
- Top-10 users by betweenness all have degree 50 (the cap), but betweenness varies from
  255k to 79k: betweenness separates users that degree cannot.
- Limitations: sampled betweenness is approximate; Spearman 0.989 is inflated by the many
  degree-1 nodes with betweenness 0.

## Part B conclusion
- The network is a connected small world (average path 4.56), strongly hub-dominated
  (top 10% of artists hold 70% of listens), with real social clustering
  (friend graph has 48.6x more triangles than random), and with bridge artists that
  differ from the most popular ones.