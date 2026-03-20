# SimASM Notebooks for WSC conference paper titled "On Behavioral Equivalence of Discrete-Event Simulation Formalisms via Abstract State Machines Semantics"

Reproducible Jupyter notebooks for the [SimASM](https://pypi.org/project/simasm/) framework, demonstrating **stutter equivalence verification** and **Little's Law validation** across three discrete-event simulation formalisms:

- **Event Graph (EG)** -- Next-Event Time-Advance (Schruben 1983)
- **Activity Cycle Diagram (ACD)** -- Activity Scanning (Tocher 1960)
- **DEVS** -- Abstract Simulator (Zeigler et al. 2018)

## Notebooks

### Stutter Equivalence Verification

| Notebook | Models | Seeds | System |
|----------|--------|-------|--------|
| [`warehouse_three_formalism_verification.ipynb`](warehouse_three_formalism_verification.ipynb) | EG vs ACD vs DEVS | 50 | 6-station warehouse (15 servers, 21 propositions) |

Each notebook verifies all three pairwise combinations (EG-ACD, EG-DEVS, ACD-DEVS) to establish mutual stutter equivalence. Despite fundamentally different simulation algorithms, all three formalisms produce identical no-stutter traces under the same observation level W.

### Little's Law Verification (M/M/5)

| Notebook | Formalism | Algorithm |
|----------|-----------|-----------|
| [`eg_littles_law.ipynb`](eg_littles_law.ipynb) | Event Graph | Next-Event Time-Advance |
| [`acd_littles_law.ipynb`](acd_littles_law.ipynb) | ACD | Three-Phase Activity Scanning |
| [`devs_littles_law.ipynb`](devs_littles_law.ipynb) | DEVS | Abstract Simulator |

Each notebook defines an M/M/5 queue (lambda=0.8, mu=1.0), converts it from JSON to SimASM, and runs 30 replications to verify L = L_q + L_s, server utilization rho = lambda/(5*mu) = 0.16, and Little's Law L = lambda*W.

## Quick Start

```bash
pip install simasm>=0.5.3
pip install ipython matplotlib-inline
jupyter notebook
```

Open any notebook and **Run All**. Each notebook is self-contained: it defines model specifications inline as Python dicts, converts them to SimASM bytecode via `%%simasm convert`, and runs experiments or verification via `%%simasm experiment` / `%%simasm verify`.

## Warehouse Case Study

The warehouse outbound process models a 6-station system with 4 arrival streams:

```
GenZA1 --+            +--------------------------------------+
GenZA2 --+--> PickA --+                                      |
                      +--> Label --> Scan --> Pack --> Release --> Sink
GenZB1 --+--> PickB --+                                      |
GenZB2 --+            +--------------------------------------+
```

| Station | Servers | Service Rate |
|---------|---------|--------------|
| PickA   | 4       | mu = 1.0     |
| PickB   | 2       | mu = 1.0     |
| Label   | 3       | mu = 1.0     |
| Scan    | 1       | mu = 1.0     |
| Pack    | 3       | mu = 1.0     |
| Release | 2       | mu = 1.0     |

The DEVS model uses a **per-server architecture** (26 atomic models) to maintain seed-synchronized random streams across all three formalisms.

## Three-Formalism Methodology

Each formalism implements a different simulation worldview:

1. **Event Graph**: Events as vertices, scheduling/cancelling edges with delays and conditions. Algorithm: scan FEL for earliest event, execute state change, evaluate outgoing edges.
2. **ACD**: Tokens cycle between passive queues and active activities. Algorithm: three-phase scan -- attempt activity starts (B-phase), advance time (A-phase), execute completions (C-phase).
3. **DEVS**: Atomic models with internal/external transitions coupled via input/output ports. Algorithm: select imminent component, output, route, transition.

All three produce identical **no-stutter traces** under observation level W, confirming that formalism choice affects computational overhead but not observable behaviour.

## Requirements

- Python >= 3.10
- simasm >= 0.5.3
- Jupyter Notebook or JupyterLab

## References

- Schruben, L.W. (1983). Simulation Modeling with Event Graphs. *Communications of the ACM*, 26(11), 957--963.
- Tocher, K.D. (1960). *The Art of Simulation*. English Universities Press.
- Zeigler, B., 2018. *Theory of Modeling and Simulation*.

## License

MIT

