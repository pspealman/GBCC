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

### Setting up the metadata

> <hands-on-title>Upload metadata</hands-on-title>
>
> 1. **Galaxy Upload steps** {% icon tool %}: Upload the sample metadata to Galaxy from Github
>    - *Click the **Upload** icon*: Find Galaxy's **Upload** icon on the Galaxy toolbar located on the left hand of the screen.
>    - *Select the **Regular** tab*: In the pop-up window click the tab labeled **Regular**. 
>    - *Click the **Paste / Fetch Data** button*
>    - *Change **New File** to `sample_metadata.tsv`*
>    - *Copy and paste the following URL to the **Download URL Field***:  
>    - ```
>      https://github.com/pspealman/GBCC/raw/refs/heads/main/metadata/MappingFile_v2.tsv
>      ```
>

### Uploading the paired end 16S read sequences
Most 16S amplicon sequencing uses paired-end reads to sequence the variable regions of bacterial 16S ribosomal DNA. Here we use Galaxy's **Upload** to upload the paired-end FASTQ files and assign them names. 

#### A side note about FASTQ files
While the FASTQ format is described in detail [here](https://en.wikipedia.org/wiki/FASTQ_format) for our purposes it's sufficient to know that each 16S amplicon is sequenced from two different directions: Forward (or Read 1, R1) and Reverse (or Read 2, R2). These paired end reads are stored, in order, in two seperate files, marked as R1 and R2. If the order of the reads are ever changed between the two files the reads will not pair correctly and may either be removed from further analysis or the run may fail entirely. 

> <hands-on-title>Upload 16S sequences</hands-on-title>
>
> 2. **Galaxy Upload steps** {% icon tool %}: Upload 16S data to Galaxy from EBI
>    - *Click Upload*: Find Galaxy's `Upload` icon on the Galaxy toolbar located on the left hand of the screen.
>    - *Select the Rule-based tab*: In the pop-up window click the tab labeled `Rule-based`. 
>    - *Select `Collections`*: From the *Upload type* dropdown menu, select the `Collections` option
>    - *Copy and paste the following into the `Input field`*: 
>    - ```
>       V1.SRR12400122_S1_L001_R1_001.fastq.gz	https://github.com/pspealman/GBCC/raw/refs/heads/main/fastq/V1.SRR12400122_S1_L001_R1_001.fastq.gz
>       V1.SRR12400122_S1_L001_R2_001.fastq.gz	https://github.com/pspealman/GBCC/raw/refs/heads/main/fastq/V1.SRR12400122_S1_L001_R2_001.fastq.gz
>       V2.SRR12400121_S1_L001_R1_001.fastq.gz	https://github.com/pspealman/GBCC/raw/refs/heads/main/fastq/V2.SRR12400121_S1_L001_R1_001.fastq.gz
>       V2.SRR12400121_S1_L001_R2_001.fastq.gz	https://github.com/pspealman/GBCC/raw/refs/heads/main/fastq/V2.SRR12400121_S1_L001_R2_001.fastq.gz
>       S1.SRR12400125_S1_L001_R1_001.fastq.gz	https://github.com/pspealman/GBCC/raw/refs/heads/main/fastq/S1.SRR12400125_S1_L001_R1_001.fastq.gz
>       S1.SRR12400125_S1_L001_R2_001.fastq.gz	https://github.com/pspealman/GBCC/raw/refs/heads/main/fastq/S1.SRR12400125_S1_L001_R2_001.fastq.gz
>       S2.SRR12400123_S1_L001_R1_001.fastq.gz	https://github.com/pspealman/GBCC/raw/refs/heads/main/fastq/S2.SRR12400123_S1_L001_R1_001.fastq.gz
>       S2.SRR12400123_S1_L001_R2_001.fastq.gz	https://github.com/pspealman/GBCC/raw/refs/heads/main/fastq/S2.SRR12400123_S1_L001_R2_001.fastq.gz
>       S3.SRR12400124_S1_L001_R1_001.fastq.gz	https://github.com/pspealman/GBCC/raw/refs/heads/main/fastq/S3.SRR12400124_S1_L001_R1_001.fastq.gz
>       S3.SRR12400124_S1_L001_R2_001.fastq.gz	https://github.com/pspealman/GBCC/raw/refs/heads/main/fastq/S3.SRR12400124_S1_L001_R2_001.fastq.gz
>       I1.SRR12400120_S1_L001_R1_001.fastq.gz	https://github.com/pspealman/GBCC/raw/refs/heads/main/fastq/I1.SRR12400120_S1_L001_R1_001.fastq.gz
>       I1.SRR12400120_S1_L001_R2_001.fastq.gz	https://github.com/pspealman/GBCC/raw/refs/heads/main/fastq/I1.SRR12400120_S1_L001_R2_001.fastq.gz
>       I2.SRR12400119_S1_L001_R1_001.fastq.gz	https://github.com/pspealman/GBCC/raw/refs/heads/main/fastq/I2.SRR12400119_S1_L001_R1_001.fastq.gz
>       I2.SRR12400119_S1_L001_R2_001.fastq.gz	https://github.com/pspealman/GBCC/raw/refs/heads/main/fastq/I2.SRR12400119_S1_L001_R2_001.fastq.gz
>       I3.SRR12400118_S1_L001_R1_001.fastq.gz	https://github.com/pspealman/GBCC/raw/refs/heads/main/fastq/I3.SRR12400118_S1_L001_R1_001.fastq.gz
>       I3.SRR12400118_S1_L001_R2_001.fastq.gz	https://github.com/pspealman/GBCC/raw/refs/heads/main/fastq/I3.SRR12400118_S1_L001_R2_001.fastq.gz
>      ```
>      
> 3. **Add Galaxy Rules** {% icon tool %}: For the two columns of data to be correctly interpreted we need to define what each column means. 
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

### Importing the paired end 16S read sequences
Now that the data is uploaded to your Galaxy account you can import it into QIIME2 for analysis.

> <hands-on-title>Import 16S sequences into Qiime</hands-on-title>
>
> 4. **Import paired end sequences into QIIME2** {% icon tool %}: Once the collection has been uploaded to Galaxy we can import it into QIIME2 
>      - *Click on the `Tools` icon*: Find Galaxy's `Tools` icon on the Galaxy toolbar located on the left hand of the screen.
>      - *Search for `qiime2 tools import` in the `Tools` box*: Copy and paste `qiime2 tools import` into the `search tools` field
>      - *Click the `qiime2 tools import` tool*
>      - Select `SampleData[PairedEndSequencesWithQuality]` from the "Type of data to import" dropdown menu.
>      - Select `Casava One Eight Single Lane Per Sample Directory Format` from the "QIIME 2 file format to import from” dropdown menu
>      - Under "Import Sequences" verify that the "Select a mechanism" section is set to `Use collection to import`
>      - Verify that the "elements" section is set to use the `galaxy_16S_sequences` collection
>      - Finally, click the `Run Tool` button.
>    
{: .hands_on}

#### Checking the quality of the sequencing data
Not all sequencing data is the same and a little analysis can shed a lot of light on the kind of data you are working with. Now that the FASTQ files have been imported we can check the quality of the reads. As noted before each 16S amplicon has two reads; a Forward and Reverse read, and each read has three elements; a read ID, sequence, and quality score. Contemporary Illumina sequencing (CASAVA 1.8+) uses a quality scoring system (called Phred + 33) for each base in the sequence, where 0 is the worst quality to 40 being the highest quality. The quality score indicates the probablity that a nucleotide is the result of a sequencing error using the formula `Q = -10*log10(p)` [more here](https://en.wikipedia.org/wiki/Phred_quality_score). So a nucelltide with a quality score of 0 is 100% likely to be the result of a sequencing error; a 10 would be 10%; 13 would be 5% etc. 

While the Phred score is a quality measure of each base, we want to identify large scale trends in sequencing quality. To do this we can use the `demux  A systemic sequencing issue 



### Generating ASVs
Combining the paired-end reads using their overlapping sequence is an essential step for resoling each 16S variable region. We use DADA2 to perform qualit control and paired-end merging and produce **a**mplicon **s**equence **v**ariants (**ASVs**). DADA2 relies on the high-accuracy of contemporary PCR and short-read sequencing technologies that produce 99% accurate DNA sequences. DADA2 performs several steps to ensure high quality ASVs, first it filters reads that have low-quality scores, then it removes duplicated reads creating consensus sequences, it then corrects any sequence errors using quality scores and consensus sequences (*denoising*), lastly a search algorithm runs to identify any possible *PCR chimeras*; artifacts formed by the incorrect combination of two seperate read pairs. Finally, all the remaining read pairs now have high quality, de-noised, non-chimeric, overlapping sequences and they can easily me merged into high quality ASVs.   

> <hands-on-title>Running DADA2</hands-on-title>
>
> 5. **Running DADA2 on `galaxy_16S_sequences`** {% icon tool %}:  
>      - *Search for `qiime2 dada2 denoise-paired` in the `Tools` box*
>      - Select the ``galaxy_16S_sequences.gza` collection from the "demultiplexed_seqs:" dropdown menu.
>      - For the `trunc_len_f` box enter a `0`
>      - For the `trunc_len_r` box enter a `0`
>      - Click the `Run Tool` button.
> 
> 6. **Checking DADA2 denoising statistics** {% icon tool %}:
>      - *Search for `qiime2 metadata tabulate` in the `Tools` box*
>      - Select `Metadata from Artifact` from the "input: Metadata" dropdown menu
>      - Select `denoising_stats.qza` from the "Metadata Source" dropdown menu
>      - Click the `Run Tool` button.
>   
> 7. **Checking ASV sample statistics** {% icon tool %}:
>      - *Search for `qiime2 metadata tabulate` in the `Tools` box*
>      - Select `Metadata from Artifact` from the "input: Metadata" dropdown menu
>      - Select `denoising_stats.qza` from the "Metadata Source" dropdown menu
>      - Click the `Run Tool` button.
>    
{: .hands_on}

### Loading the metadata

The metadata (or mapping file) is a small, tab-delimited, file that specifies relevant information for the analysis. The one used in this study contains four columns: `sample-id` which is an identifier unique to the study; the `InputFileName` which is the name of the merged file. The other columns, `Site`, `Description`, and `Distance` all contain metadata about the sequencing data, such as the location sampled and the distance from the source of the Juliana River. 

```
sample-id	InputFileName	Site	Description	Distance
I1.SRR12400120	I1.SRR12400120	Impacted	SRR12400120	23.15
I2.SRR12400119	I2.SRR12400119	Impacted	SRR12400119	23.15
I3.SRR12400118	I3.SRR12400118	Impacted	SRR12400118	23.15
S1.SRR12400125	S1.SRR12400125	Source	SRR12400125	0
S2.SRR12400123	S2.SRR12400123	Source	SRR12400123	0
S3.SRR12400124	S3.SRR12400124	Source	SRR12400124	0
V1.SRR12400122	V1.SRR12400122	Valley	SRR12400122	7.774
V2.SRR12400121	V2.SRR12400121	Valley	SRR12400121	7.774
```


