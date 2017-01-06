# Introduction to Alignment with TopHat 

[TopHat](https://ccb.jhu.edu/software/tophat/index.shtml) is a fast splice junction mapper for RNA-Seq reads that uses [Bowtie](http://bowtie-bio.sourceforge.net/index.shtml) underneath the hood to perform memory-efficient short read alignments. Though it has been somewhat superseded by [HISAT2](http://ccb.jhu.edu/software/hisat2/index.shtml) (developed by the same group - the [Center for Computational Biology](http://ccb.jhu.edu) at Johns Hopkins University), it is still a vital component in many different sequence analysis pipelines and thus plays an important role in any bioinformatics toolkit. 

## Why TopHat?

There are a variety of different sequence mappers to use for RNA-seq data (such as Bowtie/BWA), so why use TopHat? One reason: [splicing](https://en.wikipedia.org/wiki/Alternative_splicing). Many (such as the two above) mapping software packages do not take splicing into account when they map reads, and as such may be inappropriate for a given task and a given set of data. TopHat, being a spliced read aligner **and** being relatively easy to use, thus fills a critical role when one needs to align RNA-seq reads to a reference genome and map reads over exon/intron junctions. Additionally, TopHat works at the **transcriptome** level, whereas other tools (such as Bowtie) work at the **genome** level. 


## How it Works

<p align="center">
<kbd>
  <img src="Images/tophat.jpg"/>
 </kbd>
 </p>
Image from http://genomebiology.com/2013/14/4/R36

### Step One

Input sequences are aligned to the transcriptome for your reference genome (assuming you provided a GTF/GFF file). Sequences that align to the transcriptome are retained and translated to genomic coordinates. Sequences that do not align skip ahead to step two. 

### Step Two:

Non-aligned sequences are broken into sub-fragments of *x* bases (where *x* is at least 25), and then these sub-fragments are aligned to the reference genome. If two adjacent sub-fragments align to non-adjacent genomic locations, these are used to infer splice junctions. 

For even more information related to TopHat2 (and alignment in general), read [this](https://genomebiology.biomedcentral.com/articles/10.1186/gb-2013-14-4-r36) and [this](https://insidedna.me/tutorials/view/tophat2-analysis-of-rna-expression-is). 

***

## Script

The generic script that we utilize follows this format:

```
tophat --no-coverage-search -o [OUTPUT DIRECTORY] -r 200 --max-segment-intron 5000 -p 8 -G [GTF FILE] [INPUT 1] [INPUT 2]
```

When running TopHat with the paired-end reads it is critical that the `*_1` files and the `*_2` files appear in separate comma-delimited lists ([INPUT 1] and [INPUT 2] above), and that the order of the files in the two lists is the same.

Let's explain what these arguments are:

  * `--no-coverage-search`: Disables the coverage based search for junctions.
  * `--o`: Specifies the output directory
  * `--r 200`: This is the expected (mean) inner distance between mate pairs. For, example, for paired end runs with fragments selected at 300bp, where each end is 50bp, you should set -r to be 200 (which we do). The default is 50. 
  * `--max-segment-intron 5000`: The maximum intron length that may be found during split-segment search. The default is 500000.
  * `-p 8`: Use this many threads to align reads. The default is 1. Always doublecheck your system's configuration when setting this argument.
  * `-G`: GTF file. 

