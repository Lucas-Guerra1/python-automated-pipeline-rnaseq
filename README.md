# RNA-Seq Pipeline

A graphical interface for RNA-Seq analysis automation, developed in Python with tkinter. The pipeline integrates the main bioinformatics tools into a sequential workflow, from quality control to read mapping and BAM indexing — all accessible through a simple point-and-click interface, no command line required.

## Features

- Automatic detection and splitting of interleaved FASTQ files (R1/R2 in the same file) using seqtk
- Quality control with FastQC and MultiQC
- Genome index generation with STAR
- Paired-end and single-end sample mapping with STAR
- Automatic FASTA index (.fai) generation for IGV visualization
- BAM file indexing with Samtools
- Automatic dependency checking and installation via conda
- Progress bar and detailed log for each step

## Pipeline Steps

1. **Directory setup** — creates the required folder structure
2. **Interleaved splitting** — detects and splits interleaved FASTQ files into R1/R2 using seqtk
3. **FastQC** — per-sample quality control reports
4. **MultiQC** — aggregated quality report across all samples
5. **STAR index** — builds the genome index from the reference FASTA and GTF/GFF annotation
6. **STAR mapping** — maps all samples against the reference genome
7. **MultiQC (STAR)** — aggregated mapping statistics report
8. **Samtools index** — indexes the output BAM files for downstream analysis and IGV visualization

## Dependencies

- Python 3 with tkinter
- conda (Miniconda or Miniforge)
- STAR
- FastQC
- MultiQC
- Samtools
- seqtk

All bioinformatics tools can be installed automatically through the interface using conda.

## Installation

Clone the repository:

```bash
git clone https://github.com/your-username/pipeline-rnaseq
cd pipeline-rnaseq
```

Make sure you have Python 3 and conda installed. The pipeline will handle the remaining dependencies on first run.

## Usage

```bash
python3 pipeline_rnaseq.py
```

1. On startup, the interface will automatically search for installed dependencies
2. Select the **FASTQ directory** containing your raw sequencing files
3. Select the **reference directory** containing the genome FASTA and GTF/GFF annotation files
4. Select the **output directory** for mapping results
5. Run individual steps or click **Run Full Pipeline** to execute everything sequentially

## Supported File Formats

| Type | Extensions |
|------|-----------|
| FASTQ | `.fastq`, `.fq`, `.fastq.gz`, `.fq.gz`, `.fastqsanger` |
| Reference | `.fa`, `.fasta`, `.fna` |
| Annotation | `.gtf`, `.gff` |
| Alignment | `.bam` |

## Output Files

After a successful run, the output directory will contain for each sample:

- `<sample>_Aligned.sortedByCoord.out.bam` — coordinate-sorted BAM file
- `<sample>_Aligned.sortedByCoord.out.bam.bai` — BAM index for IGV
- `<sample>_Log.final.out` — mapping statistics summary
- `<sample>_SJ.out.tab` — splice junction table

The reference directory will also contain the generated `.fai` index for use in IGV.

## Visualizing Results in IGV

1. Open IGV and load the reference genome: **Genomes → Load Genome from File** → select the `.fa` file
2. Load the BAM files: **File → Load from File** → select one or more `.bam` files
3. Optionally load the annotation: **File → Load from File** → select the `.gtf` file
4. Navigate to a region of interest using the search bar

## Notes

- The interleaved splitting step is skipped automatically if all FASTQ files already have `_R1`/`_R2` in their names
- If a sample fails during mapping (e.g. insufficient memory), the pipeline logs the error and continues with the remaining samples
- The `--limitBAMsortRAM` parameter is set to 4GB by default; adjust in the script if needed for larger genomes

## License

MIT
