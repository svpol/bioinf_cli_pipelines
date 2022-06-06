# Unique gene_type descriptors

Source: https://stepik.org/lesson/32402/step/12?unit=12385

[Dowload](https://www.gencodegenes.org/human/release_25.html) Human genome release 25 GTF annotation marked as primary and containing all chromosomes and scaffolds, but not containing alternate loci.
Calculate the number of unique `gene_type` descriptors.

Solution:

```
cut -f9 gencode.v25.primary_assembly.annotation.gtf | awk -F "; " '{ print $2 }' | grep 'gene_type' | sort | uniq | wc -l
```


# The ratio of protein-coding regions

Source: https://stepik.org/lesson/32402/step/13?unit=12385

[Dowload](https://www.gencodegenes.org/human/release_25.html) Human genome release 25 GTF annotation marked as primary and containing all chromosomes and scaffolds, but not containing alternate loci.
1. Calculate the total coverage of protein-coding regions presented in the annotation with accuracy to a single nucleotide.
2. Calculate the total coverage of protein-coding genes' exons presented in the annotation with accuracy to a single nucleotide.
3. Calculate the ratio of nucleotides covering the protein-coding regions to nucleotides of protein-coding genes' exons.

Solution:

```
awk '{if ($14 ~ /protein_coding/ && $3=="CDS") print}' gencode.v25.primary_assembly.annotation.gtf | gtf2bed | bedtools merge | awk '{sum+=($3-$2)} END {print sum}'

awk '{if ($14 ~ /protein_coding/ && $3 == "exon") print}' gencode.v25.primary_assembly.annotation.gtf | gtf2bed | bedtools merge | awk '{sum+=($3-$2)} END {print sum}'

# Then divide the second number on the first one, multiply by 100 and round to the whole.
```


# The longes exon in a protein-coding gene

Source: https://stepik.org/lesson/32402/step/14?unit=12385

[Dowload](https://www.gencodegenes.org/human/release_25.html) Human genome release 25 GTF annotation marked as primary and containing all chromosomes and scaffolds, but not containing alternate loci.
Find the longest exon being a part of a proteing-coding gene. The task is to provide the name of the gene (as an official HGNC name) and the lenght of the exon.

Solution:

```
awk '{if ($14 ~ /protein_coding/ && $3=="exon") print}' gencode.v25.primary_assembly.annotation.gtf | awk '{$6 = $5-$4; print}' | sort -nk6 -r | awk 'NR==1{print $18,$6}'
```

# C.elegans autosome length

Source: https://stepik.org/lesson/32398/step/15?unit=12379

Find and [download](http://ftp.ensembl.org/pub/release-86/fasta/caenorhabditis_elegans/dna/) Caenorhabditis elegans genome assembly 86, toplevel, unmasked. 
Index it and then calculate the total length of autosomes with accuracy to a single nucleotide.

Solution:

```
# For convenience the downloaded assembly was renamed to "ce_ens_86.fa"
samtools faidx ce_ens_86.fa
awk '{if ($1!="MtDNA" && $1!="X") sum+=$2} END {print sum}' ce_ens_86.fa.fai
```


# Maximal alignment quality

Source: https://stepik.org/lesson/32404/step/12?unit=12386

Align the data of run [SRR3535767](https://trace.ncbi.nlm.nih.gov/Traces/sra/?run=SRR3535767) to the genome of Caenorhabditis elegans ([assembly 86, toplevel, unmasked](http://ftp.ensembl.org/pub/release-86/fasta/caenorhabditis_elegans/dna/) using `bowtie2`. When this is done, you shuold receive a `*.sam` file. Name it Ce_Pol2.sam. Then do the following:
1.  Using `samtools` convert Ce_Pol2.sam to Ce_Pol2.bam, sort it by the coordinate, output to Ce_Pol2.sorted.bam file and index the sorted file.
2.  Dteremine the maximal quality of alignment.
3.  Calculate the per cent of reads in file Ce_Pol2.sorted.bam aligned with maximal alignment quality.

Solution:

```
# This is how Ce_Pol2.sam was obtained:
seqtk seq -A Ce_Pol2.fa.fastq > Ce_Pol2.fa
samtools faidx Ce_Pol2.fa
bowtie2-build ce_ens_86.fa ce_ens_86
bowtie2 -S Ce_Pol2.sam -x ce_ens_86 -U Ce_Pol2.fastq

# sam to bam convertation, sorting and indexation of bam:
samtools view -S -b Ce_Pol2.sam > Ce_Pol2.bam
samtools sort Ce_Pol2.bam -o Ce_Pol2.sorted.bam
samtools index -b Ce_Pol2.sorted.bam

# Calculating maximal quality and the number of alignments with the maximal quality:
samtools view Ce_Pol2.sorted.bam | cut -f5 | sort -nr | uniq -c | head -n 1

# Take the first number in the output above and divide it to the one below, multply by 100 and round to integer:
samtools view Ce_Pol2.sorted.bam | wc -l
```



