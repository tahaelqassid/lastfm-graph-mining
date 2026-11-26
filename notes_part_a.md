# Part A: Modeling and loading (LastFM)

## Q2. What is the network?
- Dataset: LastFM (HetRec 2011), loaded from the GDS Python client.
- Node: a User (1,892) or an Artist (18,022).
- Edges:
  - LISTEN_TO: a user listens to an artist. 92,834 edges, with a weight = number of plays.
  - IS_FRIEND: two users are friends. 12,717 edges.
  - TAGGED: a user puts a tag on an artist. 186,479 edges.
- Graph type: heterogeneous (two node types), weighted on LISTEN_TO,
  stored as directed, mostly bipartite (users link to artists)
  plus a friendship layer between users.
- Node properties: id, rawId. Edge properties: weight (LISTEN_TO), tagID and timestamp (TAGGED).
- Why not one table: one table cannot hold three kinds of links at once,
  and questions like "artists my friends listen to that I do not"
  need many joins in a table but are a simple path in a graph.

## Q3. Loading and data quality
- Loaded into Neo4j with Cypher (UNWIND + MERGE for nodes, MATCH + CREATE for edges).
- Counts verified: 1,892 users, 18,022 artists, 12,717 + 92,834 + 186,479 edges.
- Self-loops: 0, nothing to do.
- Duplicate IS_FRIEND or LISTEN_TO edges: 0, nothing to do.
- TAGGED: 186,479 rows but only 71,064 distinct (user, artist) pairs
  (about 2.6 tags per pair). Not errors, but TAGGED is not used for
  degree or popularity, to avoid counting the same pair many times.
- IS_FRIEND: 0 reciprocal edges, because each friendship is stored once.
  Decision: treat IS_FRIEND as undirected in the analysis.
- Isolated nodes: 0.
- Users who never listened: 0.
- Artists never listened to: 390 (tagged only). Kept in the database,
  excluded from popularity and recommendation analysis (17,632 artists remain).
  Possible effect: small (about 2%) on artist statistics.
- No sampling and no filtering of the data.