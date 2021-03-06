
# Bulk chip-seq data analyzing pipeline

Here is the codes for Chip-seq analyzing pipeline



## 1 Pre-processing

Pre-processing.sh includes dowloanding a batch of sra files from NCBI; converting them into fastq files; running quality control; mapping, removing duplicates and mitochondial alignments.

### 1.1 Prerequisites

(1) [Sratoolkit](https://www.ncbi.nlm.nih.gov/books/NBK158900/): download the SRA files in batch and convert them into fastq files;
    
    
(2) [Trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic): trim adpator and low quality bases;
    

(3) [Fastqc](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/): quality control of the trimmed fastq files;
  
 
(4) [Bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/index.shtml): mapping the reads to the reference genome;
    
    
(5) [Samtools](https://www.htslib.org): manipulate sam and bam files, including sam2bam conversion, sort and index;


(6) [Picard](https://broadinstitute.github.io/picard/): remove duplicates; 

### 1.2 Run the script for pre-processing
```
bash Chip_step1.sh
```
## 1.3 output

(1) ./trim: trimmed and discarded fastq files adn the trim log;                                                              

(2) ./QC: fastqc output for quality check of the fastq files;

(3) ./bam: all the processed sam and bam files; the filename with "rm" means remove mitochondrial alingment which means they are the final files that would be used for peak calling.





## 2 Peak calling

Peak_calling.sh call peaks with the "rm" named bam files in last step.

### 2.1 Prerequisites

[MACS2](https://github.com/taoliu/MACS): call peaks for chip-seq


### 2.2 Run the script for peak calling

```
sort List | parallel --jobs 5 macs2 \
callpeak -t ./trim/{}.fq.gz.sam.bam_sorted.bam_rd.bam_rm.bam \
-c ./trim/input.fq.gz.sam.bam_sorted.bam_rd.bam_rm.bam \
-f BAM -p 0.01 -g mm --outdir \
./macs --nomodel -n {} --keep-dup all -B --extsize 147 

```
## 2.3 output

./macs: peak file and pile up files, which will be used in generating bigwig file                                                              





## 3 Generate bigwig files

Generating_bw.sh is to gnerate bigwig files for visulization in tools like IGV. 

### 3.1 Prerequisites

[MACS2](https://github.com/taoliu/MACS): pileup calculation

[bedtoos](https://bedtools.readthedocs.io/en/latest/): convet bdg to bedgraph

[UCSC tools bedGraphToBigWig](http://hgdownload.soe.ucsc.edu/admin/exe/linux.x86_64/): convert bedgraph to bigwig

### 3.2 Run Generating_bw

```
bash generating_bw.sh
```
### 3.3 output

./BigWig: bigwig files that are ready for signal track visulization


## 4 Downstream analysis tools
[Homer](http://homer.ucsd.edu/homer/index.html): differential peak calling, peak annotation, H3K27ac TF imprinting peak calling;

[MACS2](https://github.com/taoliu/MACS): differential peak calling;

[deepTools](https://deeptools.readthedocs.io/en/develop/):heatmap and profile plotting; compare bigwig similarity

[ROSE](http://younglab.wi.mit.edu/super_enhancer_code.html):super-enhancer calling and annotation
 

