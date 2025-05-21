<!---
layout: tutorial_hands_on, formatted for Galaxy Training Network

title: Title of the tutorial
zenodo_link: '[!]'
questions:
- How is 16S data accessed and imported into Galaxy?
- What kind of analysis is possible using QIIME2 on Galaxy?

objectives:
- How to upload 16S pair-end reads, resolve with DADA2 and import into QIIME2
- Build diversity metrics, taxonomic assignments, and diversity metrics using QIIME2
- (Optional) Longitudinal analysis
time_estimation: '24 hours'
key_points:
- The take-home messages
- They will appear at the end of the tutorial
contributors:
- pspealman
- csantana [!]
--->
# Project Juliana
This is a tutorial for 16S environmental microbiome analysis in conjunction with GBCC: Molecular ecology at the sertão - Using Galaxy to empower research in developing communities 

## Introduction to environmental metabarcoding tutorial

Welcome to []! 

The vast majority of biodiversity in the world is at the microscopic level with the interactions of populations of bacteria, archaea, eukarya and viruses defining a microbial ecology or microbiome [Berg et al. (2020)](https://doi.org/10.1186%2Fs40168-020-00875-0)({cite:t}`berg-2020`). Importantly, the microbial populations of these microbiomes perform tasks essential for larger biomes and global health; such as nutrient turnover, element bio-availability, and carbon sequestration [Bianchi (2021)](https://doi.org/10.1007/s10533-020-00708-0)({cite:t}`bianchi-2021`). Given the complexity of microbiomes and their capacity to change, it is difficult to get a complete picture of any microbiome - but often the first step is understanding the relative abundances of bacterial and archaeal populations at the genus level. Historically, 16S amplicon sequencing (also known as metabarcoding) has been used to capture this information [Caporaso et al. (2010)](https://doi.org/10.1073/pnas.1000080107)({cite:t}`caporaso-2010`). Broadly, 16S amplicon sequencing refers to any method that uses PCR primers to amplify a highly variable regions in the otherwise deeply conserved 16S ribosomal subunit. These variable regions can then be aligned to sequences from known bacteria or archaea helping in the identification of those microbial populations.  

This tutorial aims to introduce some of the concepts and tools needed to analyze 16S amplicon sequencing data. It uses two robust and well supported computational tools for this; Galaxy, which provides a web-interface to access high perfomance computational (HPC) servers [Galaxy community (2024)](https://doi.org/10.1093/nar/gkae410)({cite:t`galaxy-2024`}) and QIIME2, a platform that runs all the computational tools needed to analyze metabarcoding data [Boylen et al. (2019)](https://doi.org/10.1038/s41587-019-0209-9)({cite:t`boylen-2019`}). 

## Background about the data sources
This tutorial uses data from [Santana et al. (2024)](https://peerj.com/articles/17900/)({cite:t}`santana-2024`). This data was collected along the length of the Juliana River basin located in the southeastern part of Bahia, Brasil in the Pratigi APA. The Juliana river runs for nearly 50 Km, starting with a river source in the Papuã Mountains running westward until it reaches the Serinhaém estuary where Ituberá is located. Three sites were sampled in this study, one was located at the source of the river (**Source**), another from a midway point along the river (**Valley**), and finally, along length of urban mangrove mudflat inside Ituberá (**Impacted**). 

## Preparing the data 

### Importing the 16S sequences
Most 16S amplicon sequencing uses paired-end reads to sequence the variable regions of bacterial 16S ribosomal DNA. Here we use Galaxy's **Upload** to upload the paired-end fastq files and assign them names. 

> <hands-on-title>Upload 16S sequences</hands-on-title>
>
> 1. **Upload steps** {% icon tool %}: Upload 16S data to Galaxy from EBI
>    - *Click Upload*: Find Galaxy's `Upload` icon on the toolbar on the left hand of the screen.
>    - *Select the Rule-based tab*: In the pop-up window click the tab labeled `Rule-based`. 
>    - *Select `Collections`*: From the *Upload type* dropdown menu, select the `Collections` option
>    - *Copy and paste the following into the `Input field`*: 
>    - ```
>      V1.SRR12400122_S1_L001_R1_001.fastq.gz	ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR124/022/SRR12400122/SRR12400122_1.fastq.gz
>      V1.SRR12400122_S1_L001_R2_001.fastq.gz	ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR124/022/SRR12400122/SRR12400122_2.fastq.gz
>      V2.SRR12400121_S1_L001_R1_001.fastq.gz	ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR124/021/SRR12400121/SRR12400121_1.fastq.gz
>      V2.SRR12400121_S1_L001_R2_001.fastq.gz	ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR124/021/SRR12400121/SRR12400121_2.fastq.gz
>      S1.SRR12400125_S1_L001_R1_001.fastq.gz	ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR124/025/SRR12400125/SRR12400125_1.fastq.gz
>      S1.SRR12400125_S1_L001_R2_001.fastq.gz	ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR124/025/SRR12400125/SRR12400125_2.fastq.gz
>      S2.SRR12400123_S1_L001_R1_001.fastq.gz	ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR124/023/SRR12400123/SRR12400123_1.fastq.gz
>      S2.SRR12400123_S1_L001_R2_001.fastq.gz	ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR124/023/SRR12400123/SRR12400123_2.fastq.gz
>      S3.SRR12400124_S1_L001_R1_001.fastq.gz	ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR124/024/SRR12400124/SRR12400124_1.fastq.gz
>      S3.SRR12400124_S1_L001_R2_001.fastq.gz	ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR124/024/SRR12400124/SRR12400124_2.fastq.gz
>      I1.SRR12400120_S1_L001_R1_001.fastq.gz	ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR124/020/SRR12400120/SRR12400120_1.fastq.gz
>      I1.SRR12400120_S1_L001_R2_001.fastq.gz	ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR124/020/SRR12400120/SRR12400120_2.fastq.gz
>      I2.SRR12400119_S1_L001_R1_001.fastq.gz	ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR124/019/SRR12400119/SRR12400119_1.fastq.gz
>      I2.SRR12400119_S1_L001_R2_001.fastq.gz	ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR124/019/SRR12400119/SRR12400119_2.fastq.gz
>      I3.SRR12400118_S1_L001_R1_001.fastq.gz	ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR124/018/SRR12400118/SRR12400118_1.fastq.gz
>      I3.SRR12400118_S1_L001_R2_001.fastq.gz	ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR124/018/SRR12400118/SRR12400118_2.fastq.gz
>      ```
> 2. **+ Rules** {% icon tool %}: For the two columns of data to be correctly interpreted we need to define what each column means. 
>      - *Click the `+ Rules` button*: The `+ Rules` button is located in left lower corner of the center panel. 
>      - *Select `Add / Modify Column Definitions` from dropdown menu*.
>      - *Click the `+ Add Definition` button*
>      - *Select `List Identifier(s)` from the dropdown menu*
>      - *Click the `Select a column` button
>      - *Select `A` from the dropdown menu*
>      - *Click the `+ Add Definition` button again*
>      - *Select `URL` from the dropdown menu*
>      - *Click the `Select a column` button
>      - *Select `B` from the dropdown menu*
>      - *Finally, click `Apply`
>      - *Change the `Name` field to `galaxy_16S_sequences`
>      - *Finally, click `Upload`
{: .hands_on}

Using the Upload Data tool:
Steps to setup data_to_import:sequences:

On the fourth tab (Rule-based):

Set “Upload data as” to Collection(s)

Set “Load tabular data from” to Pasted Table

Paste the following contents into the large text area: 




The distance between these paired reads usually includes an overlap enabling the reads to be joined together. To achieve this we use DADA2 [Callahan et al. (2016)](https://doi.org/10.1038/nmeth.3869)({cite:t`callahan-2016`}).

### Loading the metadata

The metadata (or mapping file) is a small, tab-delimited, file that specifies relevant information for the analysis. The one used in this study contains four columns: `sample-id` which is an identifier unique to the study; the `InputFileName` which is the name of the merged file.

```
sample-id	InputFileName	Site	Description
I1	I1.SRR12400120	Impacted	SRR12400120
```

````{margin}
```{admonition} Jargon: feature table, feature data
:class: jargon
If terms like *feature table* and *feature data* aren't clear
right now, don't worry! They will be clear by the end of the week.
```
````



1. **The upstream tutorial** covers steps up to the generation of the feature
   table, which tallys the frequency of amplicon sequence variants (ASV) on a
   per-sample basis, and feature data which lists the sequence that defines
   each ASV in the feature table.
2. **The downstream tutorial** begins with a feature table and feature data and
   constitutes the analysis and interpretation of that information. We'll spend
   the majority of the week on the **downstream** tutorial.

The two parts of this tutorial are both dervived from the same data set
{cite:t}`liao-data-2021`. The **upstream** tutorial uses a relatively small
number of samples (n=41) and is designed to allow us to work through the most
computationally expensive steps of the analysis quickly, so you can get
experience running these steps. By working with fewer
samples, these steps can be run in just a few minutes.

The **downstream** tutorial uses the complete feature table and feature data
published in FigShare by {cite:t}`liao-data-2021`. Since that data set
contains many more samples (n=12,546) and over 550,000,000 sequences, it
would be possible very time-consumming to run the
**upstream** steps on this data interactively. We will show
how to load that data in QIIME 2, and do some filtering of the full data to
focus our work on specific samples of interest. In our case, we'll work with
the {cite:t}`taur-autofmt-2018` samples. However, the full dataset will be
available for you to filter in other ways, and to experiment with on your own.
As the authors note: _These microbiota data, combined
with the curated clinical metadata presented here, can serve as powerful
hypothesis generators for microbiome studies._
