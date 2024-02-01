---
layout: default
title: Input and Output File Formats
nav_order: 4
has_children: true
---

* TOC
{:toc}

# Input and Output File Formats

For all input files in Biofilter, lines beginning with the symbol “#” will be ignored. This is useful for
placing comments within input files that will not be a part of the analysis.

Via Biofilter 2.0 researchers can:·

* Annotate genomic location or region based data, such as results from association studies, or CNV analyses, with relevant biological knowledge for deeper interpretation.
* Filter genomic location or region based data on biological criteria, such as filtering a series SNPs to retain only SNPs present in specific genes within specific pathways of interest.  
* Generate Predictive Models for gene-gene, SNP-SNP, or CNV-CNV interactions based on biological information, with priority for models to be tested based on biological relevance, thus narrowing the search space and reducing multiple hypothesis-testing.
