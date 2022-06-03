# Unique gene_type descriptors

Source: https://stepik.org/lesson/32402/step/12?unit=12385

[Dowload](https://www.gencodegenes.org/human/release_25.html) Human genome release 25 GTF annotation marked as primary and containing all chromosomes and scaffolds, but not containing alternate loci.
Calculate the number of unique `gene_type` descriptors.

Solution:

```
cut -f9 gencode.v25.primary_assembly.annotation.gtf | awk -F "; " '{ print $2 }' | grep 'gene_type' | sort | uniq | wc -l
```
