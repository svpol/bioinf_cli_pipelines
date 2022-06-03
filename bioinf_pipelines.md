# Unique gene_type descriptors

Source: https://stepik.org/lesson/32402/step/12?unit=12385

[Dowload](https://www.gencodegenes.org/human/release_25.html) Human genome release 25 GTF annotation marked as primary and containing all chromosomes and scaffolds, but not containing alternate loci.
Calculate the number of unique `gene_type` descriptors.

Solution:

```
cut -f9 gencode.v25.primary_assembly.annotation.gtf | awk -F "; " '{ print $2 }' | grep 'gene_type' | sort | uniq | wc -l
```


# The longes exon in a protein-coding gene

Source: https://stepik.org/lesson/32402/step/14?unit=12385

[Dowload](https://www.gencodegenes.org/human/release_25.html) Human genome release 25 GTF annotation marked as primary and containing all chromosomes and scaffolds, but not containing alternate loci.
Find the longest exon being a part of a proteing-coding gene. The task is to provide the name of the gene (as an official HGNC name) and the lenght of the exon.

Solution:

```
awk '{if ($14 ~ /protein_coding/) print}' gencode.v25.primary_assembly.annotation.gtf | awk '{if ($3 == "exon") print}' | gtf2bed | awk '{$4 = $3-$2; print}' | sort -nk4 -r | awk 'NR==1{print $19,$4}'
```
