# QC: PFM-NSI (Network Similarity Index) — TODO

Data-quality check for the PFM data, from Lynch et al. (Neuron 2026, https://www.sciencedirect.com/science/article/pii/S0896627326004125).
Package: `~/git/pfm-nsi` (https://github.com/cjl2007/pfm-nsi), installed in `numrefields` → CLI `pfm-nsi`.

## Status (2026-11-02)
- Tested on ds-asd sub-01: works out of the box, ~6 s/subject.
  Result: median NSI = 0.514, P(PFM-usable) = 0.80 [0.73, 0.87] → "Moderate-high".

## How to run
Input = the step-02 concat CIFTI (same timeseries step 03 / Infomap uses):

```bash
conda run -n numrefields pfm-nsi run \
  --cifti <OUTPUT_ROOT>/sub-XX/cifti/sub-XX_ses-1_space-fsLR32k_bold_concat.dtseries.nii \
  --outdir <OUTPUT_ROOT>/sub-XX/qc_nsi --prefix sub-XX --sparse-frac 0.25
```

- fsLR 32k = package default (no `--fsaverage6`).
- Cortex-only CIFTI is fine (priors are cortex; missing subcortical structures are skipped).
- Censored frames (FD > 0.2) are already excised in step 01 → NSI scores only the frames we keep.
- `--sparse-frac 0.25` is the package recommendation for T ≤ 2000 frames.
- Key outputs: `*_usability.json` (`summary_nsi`, `p_hat`, `ci95`, `decision`), `*_network_hist_summary.csv`, histogram PNGs.

## Caveats when interpreting
1. **Smoothing:** the concat CIFTI is already smoothed (σ = 2.55 mm), which likely inflates NSI a bit.
   Check this by also running it on the unsmoothed per-run CIFTIs in `cifti/tmp/run-*/` for a few subjects.
2. **Task data, no task regression:** ds-asd = `chase` task (TR 2.33 s); DNumRisk etc. = `magjudge`.
   Task effects are not regressed out in step 01. That's fine for QC (it scores what Infomap sees),
   but **compare NSI only within a dataset** (e.g. ASD vs TD), not across datasets/tasks.
3. **Usability cutoffs** (P = 0.5/0.8/0.9 ↔ NSI ≈ 0.41/0.52/0.58): check in the paper what data the
   priors and usability model were trained on (rest vs task) before treating them as absolute thresholds.

## Next steps
- [ ] Add pipeline step (e.g. `09_qc_nsi.py <subject>`) that reads paths/`TASK` from `config.py`
      → works for every dataset via `PFM_DATASET`.
- [ ] Collect a group table: subject, group, summary_nsi, p_hat, ci95, decision, retained minutes
      (from step-01 scrub masks), n runs.
- [ ] Plots: NSI vs retained minutes; NSI by group (ASD vs TD / DD vs TD).
- [ ] Optional: smoothed vs unsmoothed comparison (caveat 1).
- [ ] Bucket list: task-regressed variant of step 01 → how much does NSI change?
