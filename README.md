# Do scene-graph relations help anticipate the next surgical instrument?

Surgical assistance systems must anticipate an instrument before it is needed, not merely
recognise it once it appears. This small study asks whether the relations in a dynamic
surgical scene graph add predictive information beyond the recent history of visible
entities.

The work is inspired by MITI's instrument-anticipation research and uses the public CAT-SG
annotations. It is a focused mechanism study, not a reproduction of MITI's in-house results
and not evidence for clinical deployment.

## Question

> At a fixed three-second prediction horizon, do scene-graph relations improve prediction of
> the next appearing instrument beyond instrument-presence history alone?

An anticipation event is the first appearance of an instrument after it has been absent for
the complete observation and forecast interval. The model observes five seconds of history,
ending three seconds before the event. This prevents the target instrument from appearing in
the model input.

## Study design

All conditions use the same event definitions, procedure-level splits, training budget and
random seeds.

| Condition | Information available |
|---|---|
| Frequency / Markov | Population and recent instrument-transition frequencies |
| Entity GRU | Five seconds of entity-presence history, without relations |
| Temporal GNN | The same entities plus geometric and semantic relations |
| Edge control | Temporal GNN with relation information removed or shuffled |

Primary metrics are top-1 accuracy, top-3 accuracy and mean reciprocal rank. Results are
reported over three fixed seeds. The official CAT-SG split is preserved at procedure level:
25 training, 5 validation and 20 test procedures.

The primary comparison is **Temporal GNN versus Entity GRU**. The edge control decides
whether any gain is attributable to relational structure rather than model capacity.

## Scope

This repository deliberately studies one question at one prediction horizon. The following
are valuable extensions, but not part of the primary experiment:

- surgeon-specific personalisation;
- multiple anticipation horizons;
- phase-specific or technique-specific analysis;
- prediction timing, abstention and premature robotic actions;
- learning scene graphs directly from video.

## Notebooks

| Notebook | Purpose |
|---|---|
| `01_define_the_task.ipynb` | Audit CAT-SG, define anticipation events using training data only, and freeze the evaluation cohort. |
| `02_do_relations_help.ipynb` | Train the baselines and graph model, run the edge control, and report the predefined comparison. |

The notebooks are written as concise, executable narratives. Findings are added only after
the corresponding experiment has been run.

## Data

[CAT-SG](https://github.com/felixholm/CAT-SG) contains frame-level entities, geometric and
semantic relations, positions, sizes, surgical steps and technique labels for 50 cataract
procedures. Its annotations are released under CC BY-NC 4.0.

The dataset is not redistributed by this repository. Clone or copy CAT-SG's downloaded files
to:

```text
data/annotations/all/
data/annotations/splits/
```

`data/` is gitignored. The underlying surgical videos are not required.

## Running

Dependencies are managed with [uv](https://docs.astral.sh/uv/):

```bash
uv sync
uv run jupyter lab notebooks/
```

The final repository will commit the executed notebooks, figures and compact result files,
but not data or trained model weights.

## Repository layout

```text
.
├── notebooks/     # narrative analysis and experiment
├── figures/       # figures used in the write-up
├── results/       # compact machine-readable results
├── data/          # local CAT-SG annotations; gitignored
├── pyproject.toml
└── README.md
```

## References

- Holm et al. *CAT-SG: A Large Dynamic Scene Graph Dataset for Fine-Grained Understanding
  of Cataract Surgery.* MICCAI 2025. [DOI](https://doi.org/10.1007/978-3-032-05114-1_10)
- Wagner et al. *Robotic scrub nurse to anticipate surgical instruments based on real-time
  laparoscopic video analysis.* Communications Medicine, 2024.
  [DOI](https://doi.org/10.1038/s43856-024-00581-0)
- Wagner et al. *Towards multimodal graph neural networks for surgical instrument
  anticipation.* IJCARS, 2024. [DOI](https://doi.org/10.1007/s11548-024-03226-8)
- Wagner et al. *Modeling surgeon-specific instrument usage profiles to improve instrument
  anticipation.* Proc AUTOMED, 2026.
  [DOI](https://doi.org/10.18416/AUTOMED.2026.2482)
