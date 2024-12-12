# bacNeo-2.0

## Introduction

The tool **bacNeo-2.0** aims to identify potential intratumour bacteria-derived neoantigens. 

To accomplish the whole antigen-discovery processes, **bacNeo-2.0** requires raw genome, transcriptome, and/or proteome data sets.

The steps for processing sequencing data are modular and independent. Therefore, you could use `bacc` and `bach` commands, as well as other scripts in `./utils` independently to meet your specific needs.

To successfully call the commends, please add the directory path of this tool to your environment `PATH`.

## Commands

- `bacc` can extract the number of bacterial reads from genome or transcriptome datasets. See usage: `bacc -h`.

    ```
    Usage: bacc [ -1 FQ1 ] [ -2 FQ2 ] [-m OMICS] [ -g ZIP ] [ -r REF ] [ -o OUT ] [ -t THREADS ] [ -k KRAKENDB ] [-l TAXONOMY]
        -1 Paired-end clean data (R1) in fastq format.
        -2 Paired-end clean data (R2) in fastq format.
        -m Type of omics data. 'RNA' for transcriptome, 'WES'/'WGS' for genome.
        -g Whether the fastq file is zipped. 'y' for zipped (.fastq.gz format), 'n' for unzipped (.fastq format).
        -r Reference directory path for hisat2 alignment (if omics data is RNA-seq) or bwa alignment (if omics data is WES / WGS).
        -o Output directory path.
        -t Number of threads (Default threads = 16).
        -k Kraken2 database directory path.
        -l Taxonomy level you would like to generate. 
           Taxonomy levels include: 'd' for Domain, 'p' for Phylum, 'c' for Class, 'o'for Order, 'f' for Family, 'g' for Genus, and 's' for Species. 
           Please ensure the level(s) you input is(are) included above. 
           If you would like to calculate bacterial counts in multiple levels, you could input the characters one by one, e.g., -l g -l s.
        If you have multiple sample files and want to run this command repeatedly, it is recommended to make independent directory for each sample.
    ```

    Test `bacc` using RNA-seq data in `./testdata/RNA-seq` and reference data in `./reference/` with the following command to generate bacterial counts in genus and species levels in `./output/bacc`:

    ```
    bacc -1 testdata/RNA-seq/T001_R1.fq.gz -2 testdata/RNA-seq/T001_R2.fq.gz -m RNA -g y -r reference/hisat/hg38 -o output/bacc -t 16 -k reference/krakendb -l g -l s
    ```
    
    The results should be identical to the original outputs in `./output/bacc`.
    
- `bach` can predict HLA alleles for each patient sample from genome datasets. See usage: `bach -h`.

    ```
    Usage: bach [ -1 FQ1 ] [ -2 FQ2 ] [ -r REF ] [ -s SCAN ] [ -d DB ] [ -g GENES ] [ -o OUT ] [ -t THREADS ]
        -1 Paired-end clean data (R1) in fastq format.
        -2 Paired-end clean data (R2) in fastq format.
        -r Reference fasta file for bwa alignment, either hg38 or hg19.
        -s The directory path which you would like to install hla-scan into.
        -d Database directory path. It's in 'reference' our tool package, named 'HLA-ALL.IMGT'.
        -g The name(s) of HLA type(s).
            HLA types include: HLA-A, HLA-B, HLA-C, HLA-E, HLA-F, HLA-G, MICA, MICB, HLA-DMA, HLA-DMB, HLA-DOA, HLA-DOB, HLA-DPA1, HLA-DPB1, HLA-DQA1, HLA-DQB1, HLA-DRA, HLA-DRB1, HLA-DRB5, TAP1, and TAP2.
            We recommend you use HLA class I types (A, B, and C), if your are interested in intra-tumour bacterial neoantigens.
            If you would like to impute multiple HLA types at once, you could input the types one by one, e.g., -g HLA-A -g HLA-B.
        -o Output directory path.
        -t Number of threads (Default threads = 16).
        If you have multiple sample files and want to run this command repeatedly, it is recommended to make independent directory for each sample.
    ```

- `bacp` can detect bacterial peptides from proteome datasets and predict HLA-peptide affinities based on results from `bach`. The reference should be provided by users. If no proteome dataset available, `bacp` could also use 10-mer winder sliding to chop up reference proteome of interested species, and predict HLA-peptide affinities as well. See usage: `bacp -h`.

## Requirments

### For `bacc`

- If the input data is RNA-seq data: [hisat2](https://daehwankimlab.github.io/hisat2/)

- If the input data is WES/WGS data: [bwa](https://github.com/lh3/bwa)

- [samtools](https://www.htslib.org/)

- [Kraken 2](https://ccb.jhu.edu/data/kraken2_protocol/)

- python (3.7)

### For `bach`

- [bwa](https://github.com/lh3/bwa)

- [samtools](https://www.htslib.org/)

- [hlascan](https://github.com/SyntekabioTools/HLAscan/) (Optional. You could also install hlascan within `bach` by specifying the installation directory using `-s`.)

### For `bacp`

- If proteome datasets are provided: [maxquant](https://anaconda.org/bioconda/maxquant)

- [netMHCpan 4.1](https://services.healthtech.dtu.dk/services/NetMHCpan-4.1/)
