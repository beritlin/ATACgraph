# ATACgraph

![Github All Releases](https://img.shields.io/github/downloads/beritlin/ATACgraph/total.svg?style=for-the-badge)             ![issue](https://img.shields.io/github/issues/beritlin/ATACgraph?style=for-the-badge)              ![star](https://img.shields.io/github/stars/beritlin/ATACgraph?style=for-the-badge) 

ATACgraph is a simple and effective software for the analysis of ATAC-Seq data. It contains 12 analyses to profile (epi)genome. 



**:zap: UPDATE**

- Peakcalling using MACS3 and add allowing input path argument to tool

- geneplot .pdf files will located in the same path with input.bw and name as  input.bw_plot.pdf 

- enrichment plot has changed y-axis scale

- add Docker image for python version (using MACS2)

  

# Citations
If you use ATACgraph for your analysis, please cite it using the following doi: https://doi.org/10.3389/fgene.2020.618478


# ATACgraph Pipeline
![ATACgraph](https://github.com/beritlin/ATACgraph/blob/master/Figures/pipeline.png)



# <a name="SystemRequirements"></a>System Requirements
* Linux or Mac OS Environment
* Python 2.7
* deepTools 3.3.0
* [BEDtools](http://bedtools.readthedocs.org/) 
* [IDR](https://github.com/nboley/idr)
```
IDR require python 3.5
```

* [MACS3](https://github.com/taoliu/MACS) 
* Python Modules:
  * Numpy
  * pandas
  * pysam==0.11.2.2
  * matplotlib
  * matplotlib-venn
  * argparse
  * pybedtools
  * deepTools==3.3.0
  * scikit-learn
  

> **NOTE:**
>
> Please check the [supplement](./supplement.md) if you have issues with downloading other tools.

# Tutorial 
Please follow the tutorial of example use case
* [Tutorial](https://github.com/beritlin/ATACgraph/blob/master/Tutorial.md)


# Installation
## Docker version
* [ATACgraph Galaxy](https://hub.docker.com/r/lsbnb/galaxy_atacgraph)
* [ATACgraph Python](https://hub.docker.com/r/peiyulin/atacgraph)


## Linux Command Version
1. Download the source code and install the requirements.


```
$ git clone https://github.com/beritlin/ATACgraph.git
$ cd ATACgraph
$ sudo sh ./base.txt

```

2. Add your ATACgraph path to the PATH.

(1)  Edit bash profile

``` 
$ vi ~/.bash_profile
```

   (2) Add ATAC-graph/script path to the PATH environment variable.

``` 
$ PATH=$PATH:(ATACgraph/script file path)
$ source ~/.bash_profile

```

# Running ATACgraph

## Filtering reads overlapped with blacklist region
Users can filter reads by this command before running ATACgraph if they have a blacklist.

```
bedtools intersect -v -abam input.bam -b hg19_blacklist.bed > output.bam
```

## ATACgraph modules 


```
$ ATACgraph -h
Usage: atacG <subcommand> [options]
ATACgraph sub-commands include:

00_rmChr                  Remove chrM,chrPt
01_calFragDist            Generate figures of fragments size distribution & Fast Fourier transform
02_selectFragSize         Select bam fragments size
02_gtftoBed               Transform gtf file to bed files
03_callPeak               Call ATAC-seq peaks"
03_genePlot               Generate figures of depicting genes and peaks accessibility
03_junctionBed            Generate junction bed track
04_specificPeaks          Identify specific peaks between 2 groups of peaks
04_specificPeaksIDR       Identify specific peaks with Irreproducibility Discovery Rate (IDR) framework
04_specificPromoter       Identify specific promoter using Gaussian Mixture Model between 2 groups of peaks
05_seqCompare             Compare peaks between ATAC-seq and Other seq
05_compareToRNA           Comparison of accessible genes and genes expression
```

## Filtering ATAC-seq reads from any chromosome 

**Input:**
* ATAC-seq reads file in bam

```
$ ATACgraph 00_rmChr -h
Usage: 00_rmChr <input.bam> <output.bam> <chrM>

If you need to remove multiple chromosomes, use comma
to seperate. For example chrM,chrPt

```
**Output:**
* ATAC-seq bam file after removing mitochondria chromosome

## Fragment length distribution and Fast Fourier Transform (FFT)

**Input:**
* ATAC-seq bam file after removing mitochondria chromosome


```
$ ATACgraph 01_calFragDist -h
usage: 01_calFragDist [-h]  input_bam fragment_distribution_outname
                      fragment_fft_outname

positional arguments:
  input_bam
  fragment_distribution_outname
  fragment_fft_outname

optional arguments:
  -h, --help   show this help message and exit
                        
```

**Output:** 
* 2 figures (fragment lenth distribution & FFT analysis result)

   * fragment distribution
   
   ![fragDist](https://github.com/RitataLU/ATACgraph/blob/master/fragDist.png)
   
   * fragment distribution FFT
   
   ![FFT](https://github.com/RitataLU/ATACgraph/blob/master/FFT.png)


**Output:** 
* filtered bam file 

## Selectiing fragments size 

**Input:**
* ATAC-seq reads file in bam

```
$ ATACgraph 02_selectFragSize -h
usage: 02_selectFragSize [-h] [-f FILTER] [-m MODE] input_bam output_bam

positional arguments:
  input_bam
  output_bam

optional arguments:
  -h, --help            show this help message and exit
  -f FILTER, --filter FILTER
                        default=150
  -m MODE, --mode MODE  Select fragments smaller [1] or larger [2] than filter
                        size. Default=1                       

```

**Output:** 
* fragment bam file 

## Transform GTF file to BED files

**Input:**
* ATAC-seq bam file after removing mitochondria chromosome

```
$ ATACgraph 02_gtftoBed -h
usage: gtftoBed [-h] [-p PROMOTER] input_gtf gtf_name

positional arguments:
  input_gtf
  gtf_name

optional arguments:
  -h, --help            show this help message and exit
  -p PROMOTER,          --promoter PROMOTER, promoter region from gene transcript start site (TSS) default = 2000
                        
```

**Output:** 
* 11 BED file (promoter,gene,exon,intron,utr5,cds,utr3,igr) for the generating metagene plots, fold enrichment analysis 



## Generating fragment size tracks 

**Input:**

* ATAC-seq bam file after removing mitochondria chromosome

```
ATACgraph 03_junctionBed -h
usage: junctionBed [-h] [-s SEPARATE] [-b BIN] [-f FILTER]
                   input_bam output_bed

positional arguments:
  input_bam
  output_bed

optional arguments:
  -h, --help            show this help message and exit:w
  -s SEPARATE, --separate SEPARATE
                        border lenth of long and short fragment(bp), default: 150
  -b BIN, --bin BIN     binzise of genome(bp), default: 10
  -f FILTER, --filter FILTER
                        number of fragment juction tracks, default:1

```

**Output:** 

* track BED files
* Visualization on IGV

![specificPeak](https://github.com/RitataLU/ATACgraph/blob/master/Figures/Fig3A.png)

##  ATAC-seq peak calling

**Input:**
* ATAC-seq bam file

```
ATACgraph 03_callPeak -h
usage: 03_callPeak.py [-h] [-s SEPARATE] [-shift SHIFT] [-ES EXTEND]
                      [-bs BINSIZE] [-c CONTROL_BAM]
                      input_bam output_name gene_bed

positional arguments:
  input_bam       input ATAC-seq bam file
  output_name     name for output files
  gene_bed        Gene or promoter annotation bed file, either
                  gene_body_bed6.bed or gene_promoter_bed6.bed

optional arguments:
  -h, --help      show this help message and exit
  -s SEPARATE     1: integration site; 2: full-extend fragment
  -shift SHIFT    shift size from integration site(bp), default: 50
  -ES EXTEND      extend size from integration site (bp), default: 100
  -bs BINSIZE     bin size for bigwig (bp), default: 10
  -c CONTROL_BAM  input control bam file, default: none
  -p path  				path to MACS3, default:
  
```

**Output:** 
* Peak location BED file (.narrowpeak), 
* Peak intensity bigWigfile (.coverage.bw) 
* A genes list of overlapping with peaks locations (.peak_gene_list.txt)



## Identify differential enriched ATAC-seq peaks between two conditions.

To identify differentially accessible regions, ATACgraph utilizes peak BED files and BigWig files generated by callPeak module to compute peaks abundances between two samples.

**Input:**
* 2 ATAC-seq peaks BDE files
* 2 ATAC-seq abundandance BigWig files


```
ATACgraph 04_specificPeaks -h
usage: specificPeaks [-h] [-c FOLD_CHANGE] [-p P_VALUE] input_peakAs input_peakBs input_bwAs input_bwBs output_bedA output_bedB

positional arguments:
  input_peakAs  Peak beds, seprate by comma
  input_peakBs  Peak beds, seprate by comma
  input_bwAs    BigWig files, seprate by comma
  input_bwBs    BigWig files, seprate by comma
  output_bedA
  output_bedB

optional arguments:
  -h, --help    show this help message and exit
  -c FOLD_CHANGE, --fold_change FOLD_CHANGE
                        Fold change cutoff. Default:2
  -p P_VALUE, --p_value P_VALUE
                        P-value cutoff. Default:0.05
  
```
**Output:** 

* 2 ATAC-seq specific peaks BED files


## Identify differential enriched ATAC-seq peaks between two conditions with IDR

```
idr --samples 1. narrowPeak 2.narrowPeak --output-file idr.txt
```
**Output:** 

* A txt files, format information are based on [IDR](https://github.com/nboley/idr)

```
ATACgraph 04_specificPeaks -h
usage: specificPeaks [-h] [-c FOLD_CHANGE] [-p P_VALUE] input_peakAs input_peakBs output_bedA output_bedB

positional arguments:
  input_peakAs  Peak beds, seprate by comma
  input_peakBs  Peak beds, seprate by comma
  output_bedA
  output_bedB

optional arguments:
  -h, --help    show this help message and exit
  
```
**Output:** 

* 2 ATAC-seq specific peaks BED files


## Comparing peaks between ATAC-seq and another Seq.

**Input:**
* ATAC-seq peaks BED file & another seq peaks file

```
ATACgraph  05_seqCompare -h
usage: seqCompare [-h] atac_peak other_peak ATAC_name otherPeak_name overlap_name Genome_size genes

positional arguments:
  atac_peak       ATAC-seq peak bed
  other_peak      Other seq peak bed
  ATAC_name       Name for ATAC peak
  otherPeak_name  Name for other peak
  overlap_name    Name for overlapping peak
  Genome_size     Genome size(bp)
  genes           Gene or promoter annotation file with bed6.bed format

optional arguments:
  -h, --help      show this help message and exit
```
**Output:** 
* An overlapping peaks location BED file 
* Venn diagram shows the numbers of each seq peaks and over lapping peaks with p value (hypergeometric test)
* A gene list of overlapping peaks locations between 2 seq

![Venn](https://github.com/RitataLU/ATACgraph/blob/master/venn.png)

## Comparing peaks between ATAC-seq and RNA-Seq.

**Input:**
* ATAC-seq peak containing genes in BED file & Gene expression table

```
ATACgraph  05_compareRNA -h
sage: 05_compareRNA.py [-h] [-b BINS] atac_gene RNA_seq outVenn outBar

positional arguments:
  atac_gene             ATAC-seq peak containing genes in bed
  RNA_seq               Expression table
  outVenn               Venn diagram output file name
  outBar                Barplot output file name

optional arguments:
  -h, --help            show this help message and exit
  -b BINS, --bins BINS  Number of bins; default 10
```
**Output:** 
* A barplot shows percentage of accessible genes in each expression group.
* Venn diagram shows the overlapping genes between accessible regions and highly or lowly expression genes

![Barplot](https://github.com/RitataLU/ATACgraph/blob/master/Figures/Barplot_ATAC_vs_RNA.png)
![Venn](https://github.com/RitataLU/ATACgraph/blob/master/Figures/Venn_ATAC_vs_RNA.png)


## Heatmap and metagene plots of ATAC-seq abundance, Fold enrichment analysis of open regions in genomic features 
To investigate the chromatin accessibility around genes, To investigate the chromatin accessibility around genes, ATACgraph uses the files describing the ATAC-seq peak locations and gene annotations for two types of analyse. This step requires 8 genomic feature BED files, user should run gtftoBed before this step.

**Input:**
* ATAC-seq bam file

```
ATACgraph 03_genePlot -h
usage: genePlot [-h] [-p PROMOTER] input_peak input_bigwig gtf_name

positional arguments:
  input_peak
  input_bigwig
  gtf_name

optional arguments:
  -h, --help            show this help message and exit
  -p PROMOTER, --promoter PROMOTER
  

```

**Output:** 
* 3 Figures
  * The enrichment status of accessible region in genome (Fold_Enrichment.pdf)
  * The accessibility – or read abundance – around genes (gene_body_heatmap.pdf)
  * The accessibility – or read abundance – around peaks (Peak_heatmap.pdf)

*  text files
   * The value of Heatmap depicting accessibility for gene (genebody.matrix.txt & genebody.matrix.gz)
   * The value of Heatmap depicting accessibility for peak (peak.matrix.txt & peak.matrix.gz)
   * The intersection site between 8 genomic features and peaks (8 files)
     * peakcall_peaks.narrowPeak_3utr.txt
     * peakcall_peaks.narrowPeak_exons.txt                  
     * peakcall_peaks.narrowPeak_gene_igr.txt
     * peakcall_peaks.narrowPeak_5utr.txt         
     * peakcall_peaks.narrowPeak_gene_promoter.txt
     * peakcall_peaks.narrowPeak_cds.txt   
     * peakcall_peaks.narrowPeak_gene_body.txt             
     * peakcall_peaks.narrowPeak_introns.txt



* Figures 
   * Fold enrichment analysis of open regions in genomic features (.Fold_Enrichment.png)
   ![Enrichment](https://github.com/RitataLU/ATACgraph_v2/blob/master/FoldEnrichment.png)
   
   
   * heatmap, metaplot ATAC-seq abundance related to genes     
   * heatmap, metaplot ATAC-seq abundance related toand peaks
   
   

<img align="left" width="300" height="900" src="https://github.com/RitataLU/ATACgraph/blob/master/TKO.integ_coverage.bwgene_body_heatmap.png">

   <img align="right" width="300" height="900" src="https://github.com/RitataLU/ATACgraph/blob/master/Peak_heatmap.png">



