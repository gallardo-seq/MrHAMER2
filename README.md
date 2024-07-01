# MrHAMER 2.0
### Overview
MrHAMER 2.0 is a pipeline for generating of high accuracy single molecule Nanopore reads. The pipeline accepts FASTQ-format sequence files as input and outputs a multi-fastq of corrected non-chimeric reads, aligned reads and QC stats. 
### Features
The pipeline performs the following steps:
- Reads are mapped to reference genome using [minimap2]
- Separate into amplicons
- Extract UMI sequences for all reads
- Cluster UMI sequences per amplicon using [vsearch] and compute high accuracy consensus reads
- Remove pcr-derived chimeras using [Chimera_Buster] (optional)

******************
# Getting Started
**_This pipeline has only been tested on Ubuntu 20.04.5 LTS._**
### Requirements
The following software packages must be installed prior to running:

-  [miniconda3](https://conda.io/miniconda.html) - please refer to installation [instructions](https://conda.io/projects/conda/en/latest/user-guide/install/index.html).

### Installation
After installing miniconda3, install the pipeline as follows:
```bash
git clone https://github.com/gallardo-seq/MrHAMER2
```
Change to directory:
```bash
cd MrHAMER2
```
Create conda environment with all dependencies:
```bash
conda env create -f environment.yml
```
Activate environment:
```bash
conda activate MrHAMER2
```
Install Auxiliary Python Scripts
```bash
cd lib && pip install . && cd ..
```
Copy modified Medaka files provided by MrHAMER2 to conda environment:
```bash
cp medaka_mod/* $CONDA_PREFIX/lib/python3.8/site-packages/medaka
```
Deactivate environment:
```bash
conda deactivate
```
### Testing
To test if the installation was successful run:
```bash
conda activate MrHAMER2
snakemake --cores 4 --configfile config.yml
```
 
******************
# Usage
In order for the pipeline to run, the following files/folders must be in the working directory:
| File | Location in MrHAMER2 directory|
|-------|-------------|
| Snakefile | ./Snakefile |
| config.yml | ./config.yml |

### Input
To run the pipeline the following input files are required:

| Input | Description |
|-------|-------------|
| Long-read sequences | Folder containing FASTQ files or a single concatenated FASTQ file. |
| Reference genome | FASTA file containing the reference genome. |
| Target BED file | A BED file containing the chromosome, start and end coordinate and the name of all amplicons.|
| Medaka Model file | A tar.gz file that provides the model for Medaka processing. NOTE: The medaka model file for R10.4 chemistry with e8.2 pores at 400 bps speed is provided. For more information on Medaka models, see [medaka model info]. If a different model is needed, please download from [medaka model download].

### Config File
Before starting the pipeline, confirm the following varibles are correct in the config.yml file:
| Input | Description |
|-------|-------------|
| sample_name | The name that the output folders/files will use. |
| input_fastq | The location of the long-read sequences fastq file.|
| reference_fasta | The location of the reference genome fasta file.|
| targets_bed | The location of the target BED file.|
You can also adjust the optional parameters listed below:
| Parameter | Description |
|-------|-------------|
| chimera_buster_on | If set to true, the Chimera_Buster module will be run at the end of the pipeline and will output a fastq file of nonchimeric corrected reads. |
| chimera_buster_MT | Designates the maximum number of mismatched/indel bases allowed when comparing UMIs. Changing this value will change the specificity and throughput of the Chimera_Buster module.  Default is 1. |
| chimera_buster_HS | When set to true, chimeric reads are rechecked to account for any clustering issues earlier in the pipeline. WARNING: this part of the code is slow and is only recommended for low input samples. Default is False. |
| minimap2_param | This sets the parameters that Minimap2 will use throughout the pipeline. Please refer to the manual for Minimap2 linked above for more details on the availble parameters.|
| umi_errors |  Designates the maximum differences between UMI in read and UMI pattern. |
| min_reads_per_cluster | Minimum number of reads required for a consensus read. |
| max_reads_per_cluster | Maximum number of 1D reads used for a consensus read. |
| min_overlap | Minimum overlap with target region. |
| balance_strands | If set to True, the pipeline inforces balancing of forward and reverse 1D reads in clusters. |
| medaka_model | Location of Medaka model used to compute consensus reads. |
| fwd_context | Sequence of tail on 5' end of the forward UMI primer. |
| rev_context | Sequence of tail on 5' end of the reverse UMI primer. |
| fwd_umi | Pattern of the UMI in the forward UMI primer. |
| rev_umi | Pattern of the UMI in the reverse UMI primer. |
| min_length | Minimum combined UMI length. |
| max_length | Maximum combined UMI length. |

### Running the pipeline
Once the config.yml is updated with the correct information and the MrHAMER2 environment is activated, simply run the following command:
```bash
snakemake --cores 4 --configfile config.yml
```
`--cores` specifies how many CPU cores will be used by the pipeline.

### Outputs
 The main output file created by the pipeline is:
| Output | Description |
|--------|-------------|
| {sample_name}_{target}_final.fastq | A multi-fastq of all corrected non-chimeric reads. |


After the a pipeline analysis has completed, these files can be found at `{sample_name}/{sample_name}_{target}_final.fastq` e.g. `MrHAMER2_test/MrHAMER2_test_Env_final.fastq`.

**************************
## Help
For issues or bugs, please report them on the [issues page][issues]. 

## License
MIT - Copyright (c) 2024 Christian M Gallardo and Jessica L Albert



[//]: # (These are reference links used in the body of this note and get stripped out when the markdown processor does its job. There is no need to format nicely because it shouldn't be seen. Thanks SO - http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)

   [minimap2]: <https://github.com/lh3/minimap2>
   [Chimera_Buster]: <https://github.com/JessicaA2019/Chimera_Buster>
   [issues]: <https://github.com/gallardo-seq/MrHAMER2/issues>
   [vsearch]: <https://github.com/torognes/vsearch>
   [medaka model info]: <https://github.com/nanoporetech/medaka#models>
   [medaka model download]: <https://github.com/nanoporetech/medaka/tree/master/medaka/data>


