# :crown: WTS Leukemia Königskobra :snake:

Snakemake workflow to analyse hematological malignancies in whole transcriptome data

![snakefmt](https://github.com/clinical-genomics-uppsala/wts_leukemia_konigskobra/actions/workflows/snakefmt.yaml/badge.svg?branch=develop)
![snakemake dry run](https://github.com/clinical-genomics-uppsala/wts_leukemia_konigskobra/actions/workflows/snakemake-dry-run.yaml/badge.svg?branch=develop)

[![License: GPL-3](https://img.shields.io/badge/License-GPL3-yellow.svg)](https://opensource.org/licenses/gpl-3.0.html)

## :speech_balloon: Introduction

This snakemake workflow uses modules from hydragenetics to process `.fastq` files and obtain different kind
of fusions in RNASeq data. Alongside tables containing gene fusions, the workflow produces a
multiqc report `.html` file and some qc plots. The following modules are currently part of this pipeline:

- alignment
- compression
- fusions
- prealignment
- qc

## :heavy_exclamation_mark: Dependencies

In order to use this module, the following dependencies are required:

[![hydra-genetics](https://img.shields.io/badge/hydragenetics-v0.11.0-blue)](https://github.com/hydra-genetics/)
[![pandas](https://img.shields.io/badge/pandas-1.3.1-blue)](https://pandas.pydata.org/)
[![parabricks](https://img.shields.io/badge/parabricks-3.7-blue)](https://docs.nvidia.com/clara/parabricks/3.7.0/index.html)
[![python](https://img.shields.io/badge/python-3.8-blue)](https://www.python.org/)
[![snakemake](https://img.shields.io/badge/snakemake-6.10.0-blue)](https://snakemake.readthedocs.io/en/stable/)

## :school_satchel: Preparations

### Sample and unit data

Input data should be added to
[`samples.tsv`](https://github.com/clinical-genomics-uppsala/wts_leukemia_konigskobra/blob/develop/config/samples.tsv)
and
[`units.tsv`](https://github.com/clinical-genomics-uppsala/wts_leukemia_konigskobra/blob/develop/config/units.tsv).
The following information need to be added to these files:

| Column Id | Description |
| --- | --- |
| **`samples.tsv`** |
| sample | unique sample/patient id, one per row |
| tumor_content | ratio of tumor cells to total cells |
| **`units.tsv`** |
| sample | same sample/patient id as in `samples.tsv` |
| type | data type identifier (one letter), can be one of **T**umor, **N**ormal, **R**NA |
| platform | type of sequencing platform, e.g. `NovaSeq` |
| machine | specific machine id, e.g. NovaSeq instruments have `@Axxxxx` |
| flowcell | identifer of flowcell used |
| lane | flowcell lane number |
| barcode | sequence library barcode/index, connect forward and reverse indices by `+`, e.g. `ATGC+ATGC` |
| fastq1/2 | absolute path to forward and reverse reads |
| adapter | adapter sequences to be trimmed, separated by comma |

### Reference data

Reference files should be specified in
[`config.yaml`](https://github.com/clinical-genomics-uppsala/wts_leukemia_konigskobra/blob/develop/config/config.yaml)

1. Arriba needs several resources to run properly which can be downloaded via
[this script](https://github.com/suhrig/arriba/blob/master/download_references.sh) and the appropriate blacklists
are part of the [release tarballs](https://github.com/suhrig/arriba/releases).
2. Secondly, fusioncatcher needs a reference which can be retrieved following the instuctions at
[github](https://github.com/ndaniel/fusioncatcher/blob/master/doc/manual.md#521---direct-download-of-human-build-data).
3. Mosdepth requires a `.bed` file specifying the whole exome.
4. The RSeQC scripts run with a gene model file which can be downloaded from
[sourceforge](https://sourceforge.net/projects/rseqc/files/BED/Human_Homo_sapiens/).
5. For SortMeRNA, a list of `.fasta` files, containing rRNA sequences, should be supplied. These can be retrieved
from the [SortMeRNA repository](https://github.com/biocore/sortmerna/tree/master/data). In addition, they need
to be indexed.
6. STAR requires an indexed genome `.fasta` file which should be the same as the assembly file from arriba.
7. In case of STAR-Fusion, CTAT resources can be found
[here](https://data.broadinstitute.org/Trinity/CTAT_RESOURCE_LIB/).

## :rocket: Usage

To run the workflow,
[`resources.yaml`](https://github.com/clinical-genomics-uppsala/wts_leukemia_konigskobra/blob/develop/config/resources.yaml)
is needed which defines different resources as default and for different rules.

```bash
snakemake --profile my-profile
```

### Relevant output files

| File | Description |
|---|---|
| `compression/spring/{sample}_{flowcell}_{lane}_{barcode}_{type}.spring` | compressed `.fastq` file pair |
| `fusions/arriba/{sample}_{type}.fusions.tsv` | table containing fusions called by arriba |
| `fusions/fusioncatcher/{sample}_{type}.final-list_candidate-fusion-genes.hg19.txt` | table containing fusions called by Fusioncatcher |
| `fusions/star_fusion/{sample}_{type}.star-fusion.fusion_predictions.tsv` | table containing fusions called by STAR-Fusion |
| `qc/multiqc/multiqc_RNA.html` | `.html` report from MultiQC |

## :judge: Rule Graph

![rule_graph](images/rulegraph.svg)
