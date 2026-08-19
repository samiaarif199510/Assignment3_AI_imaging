# Assignment 3 — Biomedical Image-Analysis Pipeline (Nuclei / Fluorescence Microscopy)

## What's in here
```
assignment3_pipeline.ipynb   <- main notebook, all 4 tasks, already executed
data/                        <- dataset (train/val/test/test_corrupted + metadata.csv)
outputs/figures/             <- all saved figures (used directly in the report)
outputs/results/             <- all saved JSON records, feature tables, and the
                                 aggregated task4_test_records.csv
requirements.txt
```

## How to run
```bash
pip install -r requirements.txt
jupyter nbconvert --to notebook --execute --inplace assignment3_pipeline.ipynb
```
The first code cell auto-downloads the dataset from GitHub into `data/` if it isn't
already there, so the notebook is self-contained.

## About the LLM (Ollama) steps
Tasks 1, 2 and 4 call a **local** Ollama server, exactly as in the labs:
```bash
ollama serve
ollama pull llama3.2-vision   # for the Task 1 image description
ollama pull llama3.1          # for the Task 2 / Task 4 text-only interpretation
```
If Ollama is not running, every LLM call automatically falls back to a **clearly
labelled simulated response** (`"_source": "SIMULATED (Ollama unreachable)"`,
`used_simulated_fallback: true`), built deterministically from the real numbers
computed earlier in the pipeline (object counts, areas, solidity, etc.) — so the
notebook always finishes and produces complete, non-empty outputs even without a
running LLM. **Run it with Ollama active to get genuine model outputs for your
report**, and check the `used_simulated_fallback` column/field before writing up
Task 1/2/4 to know which run you actually got.

## Design choices worth noting in the report
- EDA/description images are processed at the assignment-specified 256×256.
- The U-Net is trained on 128×128 downsamples of the same images — a deliberate
  CPU-training-speed trade-off, worth discussing in the report's critical analysis.
- The U-Net is a compact 3-level architecture (base=16 channels) trained for 15
  epochs with a combined BCE+Dice loss.
- The loss-ablation extension (BCE vs Dice vs BCE+Dice) uses a shorter 5-epoch
  budget since it's bonus work — increase `epochs` in that cell for a fuller result.

## Results from the reference run in this repo (all real, non-simulated except the LLM text)
- U-Net validation: Dice ≈ 0.93, IoU ≈ 0.87 (see `outputs/results/task3_unet_metrics.csv`)
- U-Net held-out test split: Dice ≈ 0.94, IoU ≈ 0.88 (`task4_unet_test_metrics.csv`)
- Per-test-image structured records: `outputs/results/task4_test_records.csv`
