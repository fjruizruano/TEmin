# repeatMiner
Iterative protocol for the analysis of repetitive elements in non model organisms

## Installation
- Copy script to your binaries folder.
- Dependencies:
  * BioPython [http://biopython.org/wiki/Main_Page](http://biopython.org/wiki/Main_Page)
  * RepeatMasker [http://www.repeatmasker.org/RMDownload.html](http://www.repeatmasker.org/RMDownload.html)
  * seqTK [https://github.com/lh3/seqtk](https://github.com/lh3/seqtk)
  * DeconSeq [http://deconseq.sourceforge.net](http://deconseq.sourceforge.net)
  * Trimmomatic [http://www.usadellab.org/cms/?page=trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic)

## 1. Repetitive elements mining (from the satMiner pipeline [https://github.com/fjruizruano/satminer](https://github.com/fjruizruano/satminer))

### 1a. Preparing sequences to RepeatExplorer

There is not a number of paired reads to start. You can try with 100000 or 200000. You should also indicate the minimum quality and the minimum length. For example if you are using Illumina PE 2x100 reads, you can try 20 and 100, respectively.

```
$ rexp_prepare.py NumberOfPairedReads LibraryA_1.fastq LibraryA_2.fastq MinQual MinLen
```

### 1b. Run RepeatExplorer

Run RepeatExplorer with default options.

### 1c. Get contigs

Uncompress RepeatExplorer's output and go to the "clusters" folder. Get a list with the name of the contigs representing a half of the number of the cluster reads reads.

```
$ cd seqClust/clustering/clusters
$ rexp_get_contigs.py
```
Since clusters with few number of reads are difficult to distinguish as satDNA clusters, we select a half of the clusters and we then extract the sequences as a FASTA file:

```
$ extract_seq.py FastaFile List
```

### 1d. Run DeconSeq
```
$ deconseq_run.py ListOfFastaFiles Reference Threads
```

### 1e. Prepare filtered reads to RepeatExplorer
Usually, we recommend to duplicate the number of reads.
```
$ rexp_prepare_deconseq.py NumberOfPairedReads LibraryA_clean_1.fastq LibraryA_clean_2.fastq
```
You will then get a FASTA file to run again RepeatExplorer, so continue with step 1b.

### XX. Run dnaPipeTE

```
python3 /directory/to/dnaPipeTE.py -input directory/to/reads_1.fastq -output /output/directory/ -cpu 12 -sample_number 2 -sample_size 500000 -RM_lib /directory/to/database.fasta -Trin_glue 10 -contig_length 300
```

```
cd /directory/to/dnapipete/Annotation/
grep -v "Low_complexity" one_RM_hit_per_Trinity_contigs | grep -v "Simple_repeat" | grep -v "Unknown" > one_RM_hit_per_Trinity_contigs_dante3_comp
```


```
dnapipete_createdb.py ../Trinity.fasta one_RM_hit_per_Trinity_contigs_dante3_comp

```
### XX. Abundance and divergece

```
$ repeat_masker_run_big.py ListOfFastaFiles FastaReference NumberOfThreads
```
