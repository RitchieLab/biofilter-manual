---
layout: default
title: Filtering Followed by Annotation Examples
parent: Examples
nav_order: 3
---

* TOC
{:toc}

# Filtering Followed by Annotation Examples
## Example 1: Input a SNP list and Map SNP Positions to Regions
Configuration:
```
KNOWLEDGE test.db
SNP rs11 rs12 rs13 rs14 rs15 rs16
FILTER region
```

Output:
```
#chr    region  start   stop
1       A       8       22
1       B       28      52
1       C       54      62
1       D       58      72
```

## Example 2: Map SNPs to Groups and Filter on the Source
Configuration:
```
KNOWLEDGE test.db
SOURCE paint
FILTER snp group source
```

Output:
```
#snp    group   source
rs11    cyan    paint
rs12    cyan    paint
rs15    cyan    paint
rs16    cyan    paint
```

## Example 3: Testing Overlap of SNP and Region Lists, Outputting Regions
Input Files:

**Input 1**
```
#snp
rs14
rs15
rs16
rs17
rs18
rs19
```

**Input 2**
```
#chr    region  start   stop
1       A       8       22
1       B       28      52
1       C       54      62 
1       D       58      72
```

Configuration:
```
KNOWLEDGE test.db
SNP_FILE input1
REGION_FILE input2
FILTER region
```

Output:
```
#chr    region  start   stop
1       B       28      52
1       C       54      62
1       D       58      72
```

## Example 4: Testing Overlap of Gene and Source Lists, Outputting Regions
Configuration:
```
KNOWLEDGE test.db
GENE A C E G P R
SOURCE spectrum
FILTER region
```

Output:
```
#chr    region  start   stop
3       P       14      18
3       R       44      52
```

## Example 5: Filter Gene List Based on Sources, and Output Regions
Configuration:
```
KNOWLEDGE test.db
GENE A C E G P R
SOURCE paint spectrum
FILTER gene source region
```

Output:
```
#gene   source      chr region  start   stop
A       paint       1   A       8       22
C       paint       1   C       54      62
P       spectrum    3   P       14      18
R       spectrum    3   R       44      52    
```

## Example 6: Output of Genes Found in Pathway-Based Input, Filtered by Genotyping Platform
Configuration:
```
KNOWLEDGE test.db
SNP rs11 rs12 rs13 rs14
GENE A C E G P R
GROUP cyan yellow
FILTER region
```

Output:
```
#chr    region  start   stop
1       A       8       22
```

## Example 7: Output of Genes Annotated by Group found in Pathway-Based Input, Filtered by Genotyping Platform
Configuration:
```
KNOWLEDGE test.db
SNP rs11 rs12 rs13 rs14 rs16 rs17 rs18
GENE A C E G P R
GROUP cyan yellow
FILTER gene group
```

Output:
```
#gene   group
A       cyan
C       cyan
```

