# pfm_pattern_pipeline

Precision functional mapping (Gordon et al. 2017 style) on fsLR 32k CIFTIs:
fMRIPrep outputs → denoising → surface CIFTI → vertex-wise connectivity → Infomap at
several densities → consensus networks, labelled against a reference atlas.

```bash
cd pfm
PFM_DATASET=asd python run_pipeline.py --subjects 1        # one subject, all steps
PFM_DATASET=asd python run_parallel.py --steps 04 04b 05    # many subjects at once
```

- `pfm/` pipeline scripts, `configs/` one config per dataset, `projects/` per-dataset
  notebooks, `docs/` methods and QC notes.
- Full documentation: `CLAUDE.md`; methods write-up: `docs/PFM_methods.md`.
- Reference: Gordon EM et al. (2017). Precision Functional Mapping of Individual Human Brains. *Neuron* 95(4).
