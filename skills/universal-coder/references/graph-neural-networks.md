# Graph Neural Networks

Use for: node/edge/graph classification, link prediction, and message-passing architectures
over graph-structured data (the kind of workload PyTorch Geometric targets).

## When to use

- Data is naturally a graph (molecules, social networks, knowledge graphs, meshes, citation
  networks) and relationships between nodes, not just node features alone, matter to the task.
- The task is one of: node classification, link prediction, graph classification/regression, or
  learning a graph embedding.

## Core concepts

- **Message passing** is the core primitive: each layer updates every node's representation by
  aggregating (sum/mean/max/attention) transformed features from its neighbors, then combining
  with its own previous representation. Stacking `k` layers lets information propagate `k`
  hops.
- **Over-smoothing.** Stacking too many message-passing layers makes all node representations
  converge to similar values, destroying discriminative power. Start shallow (2-4 layers) and
  only go deeper with explicit mitigations (residual connections, normalization).
- **Transductive vs. inductive.** Transductive models (classic GCN) see the full graph,
  including test nodes, at training time and don't generalize to unseen graphs. Inductive
  models (GraphSAGE-style, or anything using only local neighborhoods) generalize to new,
  unseen graphs — pick based on whether the task needs to score a brand-new graph later.

## Data representation

- Node features: a `[num_nodes, feature_dim]` tensor.
- Edges: typically a `[2, num_edges]` index tensor (source/target node indices), plus optional
  edge features/weights.
- Batch multiple small graphs by concatenating their node/edge tensors and tracking a per-node
  graph-membership vector — don't pad to a dense adjacency matrix for sparse graphs, it wastes
  memory and compute at scale.

## Workflow

1. Confirm whether the task is transductive (one fixed big graph, some labels held out) or
   inductive (many separate graphs, or a graph that will grow after deployment) — this decides
   the model family and the train/test split strategy.
2. Normalize node/edge features; graph-structured data is especially sensitive to feature scale
   because it gets propagated and aggregated across hops.
3. Start with a small number of message-passing layers and a simple aggregation; only add
   depth, attention, or heterogeneous-edge handling once the simple baseline is measured.
4. Split by graph (for inductive tasks) or by node with care for information leakage through
   edges (for transductive tasks) — a naive random split can leak test-node information into
   training through its neighbors.
5. For heterogeneous graphs (multiple node/edge types), model each edge type's message
   function separately rather than forcing one shared transformation across incompatible
   relation types.

## Pitfalls

- Using accuracy alone on imbalanced node classification (common in real graphs) — report
  per-class F1 or AUROC instead.
- Forgetting to make edges bidirectional when the underlying relationship is symmetric but the
  data was stored as directed pairs.
- Evaluating link prediction with random negative sampling that doesn't match the deployment
  distribution of negatives.

## Quick API reference (PyTorch Geometric `MessagePassing`)

```python
import torch
from torch.nn import Linear, Parameter
from torch_geometric.nn import MessagePassing
from torch_geometric.utils import add_self_loops, degree

class GCNConv(MessagePassing):
    def __init__(self, in_channels, out_channels):
        super().__init__(aggr="add")          # "add" | "mean" | "max"
        self.lin = Linear(in_channels, out_channels, bias=False)
        self.bias = Parameter(torch.empty(out_channels))

    def forward(self, x, edge_index):
        edge_index, _ = add_self_loops(edge_index, num_nodes=x.size(0))
        x = self.lin(x)
        row, col = edge_index
        deg = degree(col, x.size(0), dtype=x.dtype)
        deg_inv_sqrt = deg.pow(-0.5)
        deg_inv_sqrt[deg_inv_sqrt == float("inf")] = 0
        norm = deg_inv_sqrt[row] * deg_inv_sqrt[col]
        out = self.propagate(edge_index, x=x, norm=norm)   # orchestrates message/aggregate/update
        return out + self.bias

    def message(self, x_j, norm):          # x_j = source-node features per edge; _i = target
        return norm.view(-1, 1) * x_j
```

Any tensor passed to `propagate()` is auto-indexed in `message()` via `_i` (target) / `_j`
(source) suffixes. For bipartite graphs, pass `size=(N, M)` to `propagate()` and feed features
as a tuple `x=(x_src, x_dst)`.

## Escalating beyond this file

This covers the architectural judgment calls (depth, splits, evaluation). For the full
PyTorch Geometric API surface — heterogeneous-graph layers, explainability tooling, custom
dataset loaders, distributed/scaling patterns — defer to a dedicated PyTorch Geometric
skill/reference if one is installed and available; see `escalation-and-scope.md`.
