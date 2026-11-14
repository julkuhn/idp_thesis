# idp_thesis

Code and run configurations for the master's thesis *Large Scale Automized Binder Design
to Disordered Proteins* (Julia Kuhn).

## Layout

| | |
|---|---|
| `1_preprocessing/` | domain segmentation and IDP crop selection |
| `2_ablation/` | five-target benchmark: arm configurations, run statistics, analysis scripts |
| `3_production_up500/` | 500-crop campaign: configurations, job manifests, SLURM scripts, per-crop statistics |
| `4_validation/` | Boltz-2 re-prediction: inputs, scores, analysis script |
| `5_calvados/` | CALVADOS analysis scripts and summary outputs |
| `bindcraft_code/` | the BindCraft 1 IDP fork, the BindCraft 2 baseline, and the per-arm configuration JSONs |

## Running a design

Every arm runs the same code and is defined by its JSON configuration:

```bash
python bindcraft.py --settings <target>.json --advanced <arm>.json --filters <filters>.json
```

Arm configurations are in `bindcraft_code/ablation_arm_configs/`. The production
configuration is `3_production_up500/advanced_actif_uncapped.json` with
`3_production_up500/filters_idp.json`. Job manifests are tab-separated,
`<settings>\t<advanced>\t<filters>`, one line per crop.

Analysis scripts need numpy, scipy and matplotlib. They carry absolute paths from the
cluster the work ran on; repoint the `ROOT` / `OUT` variable at the top of a script before
running it. Versions are in [`REPRODUCIBILITY_VERSIONS.md`](REPRODUCIBILITY_VERSIONS.md).

`bindcraft_code/bindcraft1_idp_fork/` is the modified copy of BindCraft for IDPs and
`bindcraft_code/bindcraft2_v1.0.4/` an unmodified copy of BindCraft 2
