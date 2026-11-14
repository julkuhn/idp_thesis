# BindCraft code — the ablation versions

Both codebases used in this thesis, source only. **No model weights are included**
(AF2 params were a symlink; BC2's 77 MB ProteinMPNN weights are excluded) — see
Appendix Table 7.9 of the thesis for the exact weight versions.

```
idp_bindcraft/        BindCraft1 + the IDP modifications  (4.1 MB, 12 py)
bindcraft2_v1.0.4/          BindCraft2 baseline                 (8.2 MB, 73 py)
ablation_arm_configs/       what actually defines each arm
```

| Codebase | Revision |
|---|---|
| `idp_bindcraft` | `572aba072eafd6d1daa492c4d737058a87eb7432` (2026-10-02) |
| `bindcraft2_v1.0.4` | `a8d0f2002df373842b86a3c20c5a060c5cfdf980` (2026-09-29) |

## There is no separate code per ablation arm

Every Panel A / Panel B arm runs the **same** `idp_bindcraft` code. An arm is defined
purely by its **advanced-settings JSON** (and, for the filter arms, its filter JSON):

```
python bindcraft.py --settings <target>.json --advanced <arm>.json --filters <filters>.json
```

`ablation_arm_configs/`:

| Folder | Contents |
|---|---|
| `panelA_advanced/` | 13 arm configs — losses (`no_losses`, `no_pae_loss`, `no_plddt_loss`), template (`with_template`), objectives (`actif_w1_lam0`, `becraft_w1_lam0`, `energy_*`), filter arms (`ipsae`, `ipsae_replace`, `vanilla_filters`), and the `full` baseline |
| `panelB_advanced/` | 5 arm configs — crop/binder length and validation model (`multimer`, `monomer_validation_only`, `actif_w1_lam1`) |
| `filters/` | 5 acceptance-filter JSONs: `idp_final_filters_iface_ipae` (default), `default_filters` (vanilla BindCraft), `filters_ipsae`, `filters_ipsae_replace`, `filters_idp` (production) |
| `cmp50_advanced/` | the budget-matched BC1-vs-BC2 comparison config |

Each arm changes exactly one factor against its baseline — verified by diffing the JSONs.
**One documented exception:** the Panel B length/crop/validation arms use the standard
i_pTM objective (`multimer.json`) while the Panel B reference `actif_w1_lam1` uses the actif
objective, so comparisons against that reference carry an objective change as well as the
intended one. See the ablation notes in the reproducibility bundle.

## Which engine ran what

- **Panel A, Panel B, the 500-crop production, the conformation runs** → `idp_bindcraft`
- **BC2 arms** (cmp50 V1/V2/V3, the V7/V8 IDP-head ports, the FASTA-IDR mode) → `bindcraft2_v1.0.4`

BC1 writes `final_design_stats.csv` + PDB complexes; BC2 writes `3_Ranked/!_Ranked.csv` + mmCIF.
The metric names differ between them (`i_pAE_iface` is BC1-only; `i_pDAE` is BC2-only), which is
why cross-engine comparisons in the thesis use global `i_pTM` or Boltz-rescored ipSAE.

## Environment

BC1 runs inside the `jax_env` container (`conda activate BindCraft`); BC2 has its own
`.venv` (excluded here — recreate with `pyproject.toml` / `install.sh`).

Also excluded: BC2's vendored `uv` installer (`.bindcraft-tools/`, 61 MB — `install.sh`
re-fetches it). **Kept deliberately:** `idp_bindcraft/functions/dssp` (10 MB) — the
exact DSSP 3.1.4 executable used for all secondary-structure scoring. It is an
**aarch64 Linux** binary; on another architecture replace it with your own DSSP 3.1.4.
