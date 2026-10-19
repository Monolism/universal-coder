# Graph / Network Analysis

Use for: dependency graphs, call graphs, social networks, and other general-purpose graph
analysis (shortest paths, centrality, community detection, flow) — the kind of workload
NetworkX-style libraries target, as distinct from graph *learning* (see
`graph-neural-networks.md`).

## When to use

- The task is algorithmic over a graph's structure directly (not learning a model from it):
  "who are the most central nodes," "what's the shortest path," "find the communities/clusters,"
  "is this graph connected," "find cycles."

## Core concepts

- **Pick the right graph type first.** Directed vs. undirected, weighted vs. unweighted,
  simple vs. multigraph — each algorithm assumes one of these, and running an undirected
  algorithm on a directed graph (or vice versa) silently gives a wrong answer, not an error.
- **Centrality measures answer different questions.** Degree centrality = "how connected";
  betweenness centrality = "how much of a bridge/bottleneck"; closeness centrality = "how
  quickly can this node reach everything else"; eigenvector/PageRank-style centrality = "how
  connected to things that are themselves well-connected." Picking the wrong one for the
  question asked is the most common mistake in this domain.
- **Sparse vs. dense representation.** Real-world graphs (social, dependency, call graphs) are
  almost always sparse — use adjacency-list/edge-list representations and sparse-aware
  algorithms, never a dense `n x n` matrix for anything beyond a few thousand nodes.

## Workflow

1. State the graph's type (directed/undirected, weighted/unweighted) explicitly before picking
   an algorithm.
2. Check basic structural properties first (connected? how many components? any cycles?) —
   these often answer the real question faster than a sophisticated algorithm, and they catch
   data-quality problems (e.g. an accidentally disconnected graph) before they corrupt a later
   analysis.
3. Match the algorithm to the question: reachability → BFS/DFS; shortest weighted path →
   Dijkstra (non-negative weights) or Bellman-Ford (negative weights allowed); bottleneck
   identification → betweenness centrality or min-cut; clustering/community structure →
   modularity-based community detection (e.g. Louvain) rather than generic k-means on node
   features.
4. For large graphs, prefer algorithms with known complexity bounds appropriate to the graph
   size, and sample/approximate (e.g. approximate betweenness) rather than running an
   exact-but-quadratic-or-worse algorithm on a graph with millions of edges.

## Pitfalls

- Running centrality or shortest-path algorithms on a disconnected graph without first checking
  for disconnection — results for unreachable node pairs are often silently infinite or
  undefined depending on the library.
- Double-counting edges in an undirected multigraph when computing degree.
- Ignoring edge weights when the real-world relationship clearly has varying strength
  (treating a "mentioned once" and "collaborated 50 times" edge as equal).

## Quick API reference (NetworkX)

```python
import networkx as nx

# Shortest paths
path   = nx.shortest_path(G, source=1, target=5, weight="weight")      # Dijkstra
path   = nx.bellman_ford_path(G, source=1, target=5, weight="weight")  # negative weights ok
path   = nx.astar_path(G, source=1, target=5, heuristic=h, weight="weight")

# Structural checks — run these before anything fancier
nx.is_connected(G)                       # undirected
nx.is_strongly_connected(G)              # directed, mutually reachable
nx.is_weakly_connected(G)                # directed, ignoring direction
components = list(nx.connected_components(G))
largest    = max(components, key=len)

# Centrality — pick by the question, not by default
nx.degree_centrality(G)        # "how connected"
nx.betweenness_centrality(G)   # "how much of a bridge/bottleneck"
nx.closeness_centrality(G)     # "how fast can this node reach everything"
nx.eigenvector_centrality(G)   # "connected to things that are themselves well-connected"
nx.pagerank(G)                 # directed variant of the above

# Community detection — don't reach for k-means on node features instead
communities = nx.community.louvain_communities(G, weight="weight")

# Cuts / bottlenecks
nx.minimum_node_cut(G, s=1, t=5)
nx.minimum_edge_cut(G, s=1, t=5)
```

## Escalating beyond this file

This file covers the algorithm-selection judgment calls; it is not a full NetworkX API
reference. If a fuller NetworkX skill/reference (generators, I/O, layout/visualization
specifics) is installed and available, prefer it for anything beyond the calls above — see
`escalation-and-scope.md`.
