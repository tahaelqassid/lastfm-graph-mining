# Part C: Communities (LastFM listening graph, undirected, unweighted)

Method note: users and artists are in the same graph; 390 tag-only artists
are isolated and form singleton communities (excluded from statistics).
Louvain and LPA run in GDS on the projection "listen_c" (19,914 nodes, 185,668 relationships).

## Q8. Louvain and Label Propagation
- Louvain: 25 communities with >1 node (415 incl. 390 singletons), modularity 0.5226.
  Sizes: 3,791 / 3,289 / 2,533 / 2,114 / 2,068 / 1,400 / 1,035 / 525 / 480 / 480 ...
  The 7 largest hold 83% of the 19,524 connected nodes.
  Interpretation: modularity above 0.5 means clear taste groups, with no giant community.
- Label Propagation: 112 communities with >1 node (502 incl. 390 singletons), modularity 0.3636.
  Sizes: 12,164 / 4,103 / 102 / 57 / 51 ...
  The giant community holds 62% of connected nodes.
  Interpretation: LPA merges most of the network into one block, so it finds much less structure.

## Q9. Comparison
- Louvain vs LPA: NMI 0.462, ARI 0.188 (partial agreement).
- Louvain stability over 5 runs: NMI about 0.66, ARI about 0.61 (not deterministic;
  stream run gave 415 communities, stats run gave 417).
- Why they differ: Louvain maximizes modularity; LPA is a local majority vote without an objective,
  floods through hubs and depends on node order.
- Limitations: modularity assumes any pair can link, but in a bipartite graph users never link to users,
  so values are pessimistic; the 390 singletons inflate NMI slightly; one Louvain run (run 1) is used as
  the reference partition for the rest of the project.

- Louvain vs LPA at community level: Louvain's pop community C3 lies 98.7% inside
  LPA's second community, so both find pop. LPA's giant community contains 96% of C1,
  88% of C2, 87% of C5 and 71% of C4 (and about half of C6 and C7): LPA merges all the
  non-pop music, Louvain separates it into interpretable genres.

## Q10. Naming the communities (Louvain run 1; no ground truth; tags used as validation)
- C1 Indie and alternative rock: 480 users, 3,311 artists. The Beatles, Muse, Radiohead, Coldplay,
  The Killers. Tags: indie, rock, alternative, british.
- C2 Pop-punk, emo and alternative metal: 359 users, 2,930 artists. Linkin Park, Green Day,
  My Chemical Romance, System of a Down. Tags: metalcore, hardcore, punk.
- C3 Mainstream pop: 485 users, 2,048 artists. Lady Gaga, Britney Spears, Rihanna, Katy Perry.
  Tags: pop, dance, rnb.
- C4 80s synth-pop and new wave: 145 users, 1,969 artists. Duran Duran, a-ha, Pet Shop Boys, ABBA.
  Tags: 80s, new wave, synthpop.
- C5 Classic rock and heavy metal: 161 users, 1,907 artists. Metallica, Queen, Led Zeppelin, AC/DC.
  Tags: classic rock, hard rock, thrash metal.
- C6 Experimental electronic: 59 users, 1,341 artists. Autechre, Amon Tobin, Plaid. Tags: ambient, idm.
- C7 Hip-hop: 54 users, 981 artists. 2Pac, Nas, Snoop Dogg, Wu-Tang Clan. Tags: hip-hop, rap.
- Validation: in each community the top tags describe the same genre as the top artists,
  so the communities are semantically meaningful.
- Limitations: no formal ground truth; tags come from the same users (not independent);
  broad tags like "rock" appear in several communities.

## Q11. Nodes between communities
- Community-to-community listening matrix (% of listens): users stay mostly in their own
  community (55.8% to 82.4% on the diagonal; pop C3 is the most insular, hip-hop C7 the most open).
  18% to 44% of each community's listening goes outside.
- C1 (indie/alternative rock) is the gateway: all other communities send 6% to 23% of their
  listens to it (C6 23.4%, C4 19.7%, C5 15.6%, C7 14.6%, C2 14.3%).
- Pop users rarely leave (82.4% inside); hip-hop (14.2%) and pop-punk (11.6%) users visit pop.
- Bridge artists (>= 50 listeners, share of listeners from other communities): James Blunt 77%,
  Ramones 73%, ABBA 73%, Queen 72%, Bon Jovi 69%, Metallica 68%, Guns N' Roses 66%.
  Examples: ABBA = 37% pop + 27% synth-pop listeners; Queen = 28% classic rock + 28% indie.
- Most local artists: teen pop (Jesse McCartney, JoJo, Vanessa Hudgens, Jennifer Lopez): about 0-2% outside listeners.
- Top-10 betweenness artists are all in C1 (Beatles, Radiohead, Muse, Depeche Mode, Coldplay, Pink Floyd)
  or C3 (Lady Gaga, Madonna, Britney Spears, Paramore): betweenness mostly reflects global popularity,
  while the outside-listener share finds mid-popularity crossover artists. Different questions.
- Why they matter: bridge artists are low-risk entry points for cross-community recommendation.
- Limitations: single Louvain run (stability about 0.66); community labels are fuzzy at the boundary;
  artists with fewer than 50 listeners excluded.

## Part C conclusion
- Louvain finds 7 large, clearly interpretable taste communities (modularity 0.52) that LPA does not
  (modularity 0.36, one giant block). Communities are leaky and C1 acts as the central gateway.