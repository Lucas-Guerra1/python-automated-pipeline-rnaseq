# RNA-Seq Pipeline — Graphical Interface

A point-and-click GUI application for RNA-seq analysis automation, built with Python and tkinter. The pipeline integrates core bioinformatics tools into a sequential workflow — from quality control to read alignment and BAM indexing — requiring no command-line experience.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
- [Pipeline Steps](#pipeline-steps)
- [Supported File Formats](#supported-file-formats)
- [Output Files](#output-files)
- [Visualizing Results in IGV](#visualizing-results-in-igv)
- [Notes](#notes)
- [Limitations](#limitations)
- [License](#license)

---

## Overview

```
FASTQ files → Interleaved Split (seqtk) → QC (FastQC) → Aggregated QC (MultiQC)
→ Genome Index (STAR) → Alignment (STAR) → Mapping QC (MultiQC) → BAM Index (Samtools)
```

---

## Features

- Automatic detection and splitting of interleaved FASTQ files (R1/R2 in the same file) using `seqtk`
- Quality control with FastQC and MultiQC
- Genome index generation with STAR
- Paired-end and single-end sample alignment with STAR
- Automatic FASTA index (`.fai`) generation for IGV visualization
- BAM file indexing with Samtools
- Automatic dependency checking and installation via Conda
- Progress bar and detailed execution log for each step

---

## Requirements

| Tool | Version | Purpose |
|---|---|---|
| Python | ≥ 3.8 | Runtime (with `tkinter`) |
| conda | any | Dependency management |
| STAR | ≥ 2.7 | Genome indexing and alignment |
| FastQC | any | Per-sample quality control |
| MultiQC | any | Aggregated quality reports |
| Samtools | any | BAM indexing |
| seqtk | any | Interleaved FASTQ splitting |

> **Operating System:** Linux only (Ubuntu, CentOS, or equivalent). Not tested on macOS or Windows.

All bioinformatics tools can be installed automatically via the GUI on first run using Conda.

---

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/your-username/pipeline-rnaseq.git
cd pipeline-rnaseq
```

### 2. Verify Python and Conda

```bash
python3 --version
conda --version
```

> If Conda is not installed, download [Miniconda](https://docs.conda.io/en/latest/miniconda.html) or [Miniforge](https://github.com/conda-forge/miniforge).

The pipeline will handle all remaining bioinformatics dependencies on first run.

---

## Usage

```bash
python3 pipeline_rnaseq.py
```

On startup, the interface will:

1. Automatically search for installed dependencies
2. Prompt you to install any missing tools via Conda

### In the GUI:

1. **Select the FASTQ directory** — folder containing raw `.fastq` / `.fastq.gz` files
2. **Select the reference directory** — folder containing the genome FASTA and GTF/GFF annotation
3. **Select the output directory** — where alignment results will be saved
4. Run individual steps using the dedicated buttons, or click **Run Full Pipeline** to execute all steps sequentially

---

## Pipeline Steps

| Step | Tool | Description |
|---|---|---|
| 1 | — | Directory setup — creates the required folder structure |
| 2 | seqtk | Interleaved splitting — detects and splits R1/R2 from interleaved FASTQ files |
| 3 | FastQC | Per-sample quality control reports |
| 4 | MultiQC | Aggregated quality report across all samples |
| 5 | STAR | Genome index generation from reference FASTA and GTF/GFF |
| 6 | STAR | Paired-end or single-end alignment against the reference |
| 7 | MultiQC | Aggregated mapping statistics report |
| 8 | Samtools | BAM file indexing for downstream analysis and IGV |

---

## Supported File Formats

| Type | Extensions |
|---|---|
| FASTQ | `.fastq`, `.fq`, `.fastq.gz`, `.fq.gz`, `.fastqsanger` |
| Reference genome | `.fa`, `.fasta`, `.fna` |
| Annotation | `.gtf`, `.gff` |
| Alignment | `.bam` |

---

## Output Files

After a successful run, the output directory will contain for each sample:

| File | Description |
|---|---|
| `<sample>_Aligned.sortedByCoord.out.bam` | Coordinate-sorted BAM file |
| `<sample>_Aligned.sortedByCoord.out.bam.bai` | BAM index for IGV |
| `<sample>_Log.final.out` | Mapping statistics summary |
| `<sample>_SJ.out.tab` | Detected splice junctions |

The reference directory will also contain the generated `.fai` index for use in IGV.

---

## Visualizing Results in IGV

1. Install IGV: `sudo apt install igv` or download from [igv.org](https://igv.org)
2. Open IGV: `igv`
3. Load the reference genome: **Genomes → Load Genome from File** → select the `.fa` file
4. Load BAM files: **File → Load from File** → select one or more `.bam` files
5. Optionally load the annotation: **File → Load from File** → select the `.gtf` file
6. Navigate to a gene of interest using the search bar

---

## Notes

- The interleaved splitting step is **skipped automatically** if all FASTQ files already contain `_R1` / `_R2` in their names
- If a sample fails during alignment (e.g., insufficient memory), the pipeline logs the error and continues with the remaining samples
- The `--limitBAMsortRAM` parameter is set to **4 GB** by default; adjust this value in the script for larger genomes
- Sudo privileges may be required during dependency installation via `apt`

---

## Limitations

- **Linux only** — not tested on macOS or Windows (WSL)
- tkinter behavior may vary across Linux distributions; ensure it is installed (`sudo apt install python3-tk`)
- Processes all samples in a single sequential run; no parallel sample execution
- Does not support stranded library protocols natively — strand-specific parameters must be set manually in the script

---

## License

MIT License — feel free to use, modify, and distribute with attribution.
