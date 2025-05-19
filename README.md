<!---
layout: tutorial_hands_on

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

## Environmental metabarcoding

Welcome to []! 

The vast majority of biodiversity in the world, and the basic functioning of Metabarcoding (or amplicon) sequencing and analysis are methods used to gain general high-level information about microbial ecologies by   

This tutorial focuses on data from [Santana et al. (2024) _Prokaryote communities along a source-to-estuary river continuum in the Brazilian Atlantic Forest_](https://peerj.com/articles/17900/)({cite:t}`santana-2024`).

This data was collected along the length of the Juliana River basin located in the southeastern part of Bahia, Brasil in the Pratigi APA. The Juliana river runs for nearly 50 Km, starting with a river source in the Papuã Mountains running westward until it reaches the Serinhaém estuary where Ituberá is located. Three sites were sampled in this study, one was located at the source of the river (Source), another from a midway point along the river (Valley), and finally, along length of urban mangrove mudflat inside Ituberá. 

### Loading the 16S amplicon sequences 
Most 16S amplicon sequencing uses paired-end reads to sequence the variable regions of bacterial 16S ribosomal DNA. 

The distance between these paired reads usually includes an overlap enabling the reads to be joined together. 

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
