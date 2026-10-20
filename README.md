# Stability-first workflow for metastatic transcriptomics: a methodological stress test on GSE193103 (AURORA)

This repository holds the analysis notebook, result tables and figures for a **methodological stress test**. It is **not** a biological validation and makes no biomarker claim.

**Question.** In a small, repeated-measures, multi-site metastatic breast cancer cohort in which preservation (FFPE vs frozen) is almost perfectly aligned with disease state, how many conventionally significant genes remain *stable* once patients are the resampling unit, preservation and host-organ admixture are modelled, and the whole procedure is calibrated on patient-structured null data?

## Workflow
Patient-level complementary-pairs stability selection (S1), preservation-aware paired model (S2), replicate ICC (S3), permutation-null calibration with the Meinshausen-Buhlmann bound (S4), and host-organ correction from normal-tissue libraries (S5), applied to two axes: A (metastatic state, paired) and B (metastatic site, blocked).

## Data (not included)
GEO series **GSE193103**, file `GSE193103_salmon_gene.matrix_RAP101_plus_Normals24.txt` (raw Salmon gene-level estimated counts). The notebook checks a raw-matrix fingerprint and stops if the file has been normalised or altered.

## Run
```bash
pip install -r requirements.txt
export AURORA_DATA=/path/to/GSE193103_salmon_gene.matrix_RAP101_plus_Normals24.txt
export AURORA_OUT=./stability_first_outputs
jupyter nbconvert --to notebook --execute notebook/StabilityFirst_metastatic_breast_submission.ipynb
```
`AURORA_QUICK=1` runs a reduced smoke test only; all reported results use the full settings. Seed: `20261015`.

## Layout
| Folder | Contents |
|---|---|
| `notebook/` | Submission notebook (code frozen; markdown documents the analysis) |
| `results/` | Frozen result tables, `run_summary.json`, `freeze_manifest.json` (SHA-256) |
| `figures/` | Methodology, null calibration, Axis B heatmap, benchmark, cascade (PDF and PNG) |

## Frozen headline numbers
27,809 genes; 99 tumour libraries from 24 patients after one QC exclusion; 15 paired patients (8 preservation-matched). 367 conventional paired hits (329 after host correction); stable genes: 11 preservation-agnostic (null mean 0.12), 1 preservation-aware (0.20), 3 after host correction (0.16); 0 pass every layer. Axis B: 389 blocked-F hits at q<0.05, none stable.

## Notes and limitations
- Thresholds are declared in code and were not tuned on the outcome; the analysis was **not formally pre-registered**.
- `results/cascade.csv` is a *sequential* filter; the paper's cascade table reports non-nested counts.
- The `Class` column in the gene tables is a display aid from gene-symbol patterns; it is not used in any call.
- Patient, site and preservation labels are parsed from library names, not a clinical table.
- Host correction assumes linear mixing; lymph node has no normal reference. No host-corrected Axis B null was run.
- `freeze_manifest.json` also lists the hash of an executed notebook that is not included here; the other listed files match.

## Verify the frozen results
```bash
cd results && python - <<'PY'
import json,hashlib,os
for k,v in json.load(open("freeze_manifest.json"))["sha256"].items():
    print(k, os.path.exists(k) and hashlib.sha256(open(k,"rb").read()).hexdigest()==v)
PY
```

## Citation
See `CITATION.cff`. Archived release: see the Zenodo badge/DOI on the repository release page.

## License
Code: MIT. Results and figures: CC BY 4.0.
