
# Mining a Real Network: communities and bridges in the LastFM graph

Data Mining Labs, UIR (Prof. Berkani Safaa). Neo4j + GDS + Python.

**Question:** do listening-taste communities exist in LastFM, and can bridge artists be used to recommend across them at a small cost in relevance?

## Headline results
| | |
|---|---|
| Network | 1,892 users, 18,022 artists, 92,834 listens, 12,717 friendships |
| Degree | 60.6% of artists have 1 listener; top 10% hold 70.0% of listens |
| Small world | average shortest path 4.56; 97.96% in one component |
| Triangles (friend graph) | 19,690 vs 405 at random (48.6x) |
| Communities | Louvain: 7 large named communities, modularity 0.52 (LPA: 0.36) |
| Recommender (400 users) | Hit@10: popularity 0.418, PPR 0.625, bridge-aware PPR 0.622; +22% communities covered |

## Repository
- `01_part_a.ipynb` to `04_part_d.ipynb`: modeling and loading, properties, communities, recommender
- `notes_part_*.md`: results and interpretations per part
- `lastfm_pipeline.py`: script version of all stages (not run end to end)
- `results/`, `figures/`: outputs; `report/`: LaTeX report

## Reproduce
1. Start Neo4j 5 with the GDS plugin (Docker):
   `docker run -d --name neo4j-lastfm -p 7474:7474 -p 7687:7687 -e NEO4J_AUTH=neo4j/$NEO4J_PASSWORD -e NEO4J_PLUGINS='["graph-data-science"]' -v ~/graph-mining/neo4j-data:/data neo4j:5`
2. `python3 -m venv .venv && source .venv/bin/activate && pip install -r requirements.txt`
3. `export NEO4J_PASSWORD=...`
4. Download `artists.dat` and `tags.dat` from the HetRec 2011 LastFM dataset (GroupLens, hetrec2011-lastfm-2k) into `data/`.
5. Run the notebooks in order. Do not rerun the loading cell: relationships would be duplicated.

Seed 42; 20% hold-out; 400 evaluation users. Dataset: Cantador, Brusilovsky, Kuflik, HetRec 2011 (non-commercial use).
EOF