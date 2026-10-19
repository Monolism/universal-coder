# Neuroimaging Data (BIDS)

Use for: organizing or validating neuroimaging datasets (MRI, fMRI, EEG, MEG) against the
Brain Imaging Data Structure (BIDS) specification.

## When to use

- A dataset of scans/recordings needs to be organized, converted, or validated against BIDS.
- Questions about BIDS directory layout, filename conventions, or required metadata sidecars.

## Core structure

BIDS organizes data as `sub-<label>/[ses-<label>/]<datatype>/sub-<label>_<...>_<suffix>.<ext>`,
e.g.:

```
sub-01/
  anat/
    sub-01_T1w.nii.gz
    sub-01_T1w.json          # sidecar metadata — required
  func/
    sub-01_task-rest_bold.nii.gz
    sub-01_task-rest_bold.json
```

Key rules:
- Every data file gets a JSON sidecar carrying acquisition metadata (scanner parameters,
  task description) — the data file alone is not self-describing.
- Entities (`sub-`, `ses-`, `task-`, `run-`, …) appear in a fixed order and must be consistent
  across all files for a given acquisition.
- A `dataset_description.json` at the root is required and declares the BIDS version and
  dataset-level metadata.
- Derivatives (processed outputs, e.g. after preprocessing) live in a separate
  `derivatives/<pipeline-name>/` tree mirroring the raw layout — never mixed into the raw tree.

## Workflow

1. Before writing a converter, identify the datatype (anat/func/dwi/eeg/meg/…) and which
   entities apply — not every entity is valid for every datatype.
2. Generate the JSON sidecar alongside every data file; don't treat it as optional metadata to
   add later — several required fields (e.g. `RepetitionTime` for BOLD) are structural, not
   cosmetic.
3. Validate the resulting dataset against the official BIDS validator before treating it as
   done — hand-written converters reliably miss edge cases the spec covers (e.g. intended-for
   fieldmap linking, inheritance rules for metadata that lives at a higher directory level).
4. For derivatives, record provenance (which pipeline, which version, which inputs) in the
   derivative's own sidecar rather than relying on directory naming alone.

## Pitfalls

- Treating session/run numbering inconsistently across subjects (BIDS requires the same
  entity structure across the dataset for machine-readability).
- Skipping the "inheritance principle" — metadata can live at a higher directory level and
  apply to everything below it; a converter that only looks at the file's own directory can
  miss inherited fields.
- Mixing raw and derivative data in the same subject tree.

## Quick reference: minimum required files + validation

```json
// dataset_description.json — the one file every BIDS dataset must have
{
  "Name": "My Study",
  "BIDSVersion": "1.9.0",
  "DatasetType": "raw"
}
```

```bash
# Validate before calling a converter done — do not trust a hand-written converter's
# own idea of correctness.
pip install bids-validator-deno   # or: npx bids-validator ./my_dataset  (legacy Node validator)
bids-validator ./my_dataset
```

```python
# PyBIDS for querying an existing BIDS dataset rather than hand-parsing filenames
from bids import BIDSLayout
layout = BIDSLayout("./my_dataset")
funcs = layout.get(datatype="func", suffix="bold", extension=".nii.gz")
```

## Escalating beyond this file

This covers directory/entity/inheritance judgment calls, not the full BIDS specification
(all datatype-specific metadata fields, the complete BEP extension list, HeuDiConv/dcm2bids
conversion recipes). If a dedicated BIDS skill/reference is installed and available, defer to
it for that depth — see `escalation-and-scope.md`.
