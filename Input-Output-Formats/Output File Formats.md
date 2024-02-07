---
layout: default
title: Output File Formats
parent: Input and Output Formats
nav_order: 2
---

* TOC
{:toc}

# Configuration Report
The format of a configuration output file is, by design, identical to a configuration input file. The details of that format can be found in the corresponding section of the [previous](https://ritchielab.github.io/biofilter-manual/Input-Output-Formats/InputOutput.html#configuration-files-not-required) chapter.

Note however that the `INCLUDE` instruction is not relevant for configuration output files because the structure of inclusions is not preserved internally. This means that even if the configuration file(s) provided to Biofilter include other configuration files, the report generated by the `REPORT_CONFIGURATION` option will not contain any `INCLUDE` instructions. Instead, all options from all included files will be merged into a single reported configuration.

# Gene and Group Name Statistics Reports
These reports list all of the types of identifiers available for genes or groups, respectively, along with some statistics about their overall uniqueness. 

Examples:
```
#type           names       unique      ambiguous
symbol          91687       89417       2270
entrez_gid      62977       62977       0
refseq_gid      48880       48880       0
refseq_pid      35719       35719       0
ensembl_gid     35719       50098       61
ensembl_pid     31225       31225       0
hgnc_id         33036       33036       0
mim_id          15446       15446       0
hprd_id         18065       18065       0
vega_id         17572       17545       27
mirbase_id      1523        1523        0
unigene_gid     25016       24131       885
uniprot_gid     101047      101047      0
uniprot_pid     105084      98839       6245
pharmgkb_gid    27062       27062       0
```

# LD Profiles Report
This report lists the LD profiles available in the [knowledge database](https://ritchielab.github.io/biofilter-manual/loki/loki.html). If LD Spline has not been used to calculate LD-adjusted gene boundaries, then only the default profile with canonical gene boundaries will be shown.

# Invalid Input Report
If the `REPORT_INVALID_INPUT` option has been enabled, then any user input data which cannot be parsed or understood by Biofilter will appear in one of these report files. A separate file is generated for each type of input (SNP, position, region, etc.), and for each invalid input line, that entire line will be copied to the corresponding report file preceded by a comment line describing the error. For example, the SNP input file on the left will yield the invalid SNP report file on the right:

```
#snp            # invalid literal for long() with base 10: 'chr5:678'
rs12            chr5:678
rs34
chr5:678
rs90
```
One of the inputs was not understood as a valid RS number, but the other three were parsed successfully and added to the input dataset.

# Analysis Outputs
Filtering, annotation and modeling analyses always return one or more tab-separated columns, but the number and contents of those columns can vary. Each analysis mode allows the user to exactly specify the desired output columns.

In the simplest case, the user can request one of the six data types which Biofilter also takes as input: SNP, position, region, gene, group or source. The output will then contain one or more columns describing the specified data type, in exactly the same format as Biofilter requires for input of the same type. For example, SNP output produces a single column of RS numbers, position output produces three columns (chromosome, label, position), and so on. More than one basic type can also be output together (and is required for annotation and modeling analyses), in which case the columns corresponding to any additional types are simply appended in order to the final output. For example, the analysis options on the left will produce the output columns on the right:

|FILTER position|chr&emsp;position&emsp;pos|
|FILTER gene snp|gene&emsp;snp|
|ANNOTATE gene region|gene&emsp;chr&emsp;region&emsp;start&emsp;stop|
|MODEL gene|gene1&emsp;gene2&emsp;score|

Note that there are two different places Biofilter could draw from when outputting any given type of data: one of the user input datasets, or the prior knowledge database. If an output type is requested which was not provided as input then the choice is clear, and Biofilter will produce the requested output based on the data contained in the knowledge database. For any data type which was provided as input, however, Biofilter will pull any corresponding output columns from the input data rather than the knowledge database.

This means, for example, that if regions are supplied as input and both “`gene`” and “`region`” are requested as output, then the result may not be as expected. The output will list both genes and regions, and the genes in the first column will indeed be the ones whose genomic region matched one of the provided input regions. However, the region shown next to each gene will not be that gene’s region, as one might hope; it will instead be the user-provided input region which matched the gene’s region.

Biofilter provides additional output options to deal with situations such as these. The six basic types will suffice for most use cases, but they are actually only shorthand for their respective sets of individual output columns. For more particular use cases there are a few additional shorthand types (such as “`generegion`”), and any single output column may also be requested individually. This includes each
separate column from any of the six data type outputs (such as “`region_chr`” which is the first of four columns included in the “`region`” output type), as well as some columns which are not included in any of the shorthand sets.

Biofilter currently supports the following outputs:

|snp|Shorthand for: snp_label|
|snp_id|The SNP’s RS number, with no prefix; if an input SNP was merged, the current (new) RS number is shown
|snp_label|The SNP’s RS number, with “rs” prefix; if an input SNP was merged, the user-provided (old) RS number is shown
|snp_extra|Any extra columns provided in the SNP input file
|position|Shorthand for: position_chr , position_label , position_pos
|position_id|An arbitrary unique ID number for the position; can be used to distinguish unlabeled positions with identical genomic locations
|position_label|The provided (or generated) label for an input position, or the RS number (with “rs” prefix) for a SNP position from the knowledge database
|position_chr|The position’s chromosome number or name
|position_pos|The position’s basepair location
|position_extra|Any extra columns provided in the position input file
|region|Shorthand for: region_chr , region_label , region_start , region_stop
|region_id|An arbitrary unique ID number for the region; can be used to distinguish unlabeled regions with identical genomic start and stop locations
|region_label|The provided (or generated) label for an input region, or the primary label for a region from the knowledge database
|region_chr|The region’s chromosome number or name
|region_start|The region’s basepair start location
|region_stop|The region’s basepair stop location
|region_extra|Any extra columns provided in the region input file 
|generegion|Shorthand for: region_chr , gene_label , region_start , region_stop Similar to “region” except that only gene regions from the knowledge database are returned, even if the user also provided input regions
|gene|Shorthand for: gene_label
|gene_id|An arbitrary unique ID number for the gene; can be used to distinguish genes with identical labels
|gene_label|The provided identifier for an input gene, or the primary label for a gene from the knowledge database
|gene_description|The gene’s descriptive text from the knowledge database, if any
|gene_identifiers|All known identifiers for the gene, of any type; formatted as “type:name\|type:name\|…”
|gene_symbols|All known “symbol”-type identifiers (symbolic aliases) for the gene, formatted as “symbol\|symbol\|…”
|gene_extra|Any extra columns provided in the gene input file
|upstream|Shorthand for: upstream_label , upstream_distance
|upstream_id|An arbitrary unique ID number for the closest upstream gene
|upstream_label|The primary label for the closest upstream gene
|upsteam_distance|The distance to the closest upstream gene
|upsteam_start|The closest upstream gene’s basepair start location
|upsteam_stop|The closest upstream gene’s basepair stop location
|downstream|Shorthand for: downstream_label , downstream_distance
|downstream_id|An arbitrary unique ID number for the closest downstream gene
|downstream_label|The primary label for the closest downstream gene
|downstream_distance|The distance to the closest downstream gene
|downstream_start|The closest downstream gene’s basepair start location
|downstream_stop|The closest downstream gene’s basepair stop location
|group|Shorthand for: group_label
|group_id|An arbitrary unique ID number for the group; can be used to distinguish groups with identical labels
|group_label|The provided identifier for an input group, or the primary label for a group from the knowledge database
|group_description|The group’s descriptive text from the knowledge database, if any
|group_identifiers|All known identifiers for the group, of any type; formatted as \|“type:name\|type:name\|…”
|group_extra|Any extra columns provided in the group input file
|source|Shorthand for: source_label
|source_id|An arbitrary unique ID number for the source; included for completeness
|source_label|The source’s name
|gwas|Shorthand for: gwas_trait , gwas_snps , gwas_orbeta , gwas_allele95ci , gwas_riskAfreq , gwas_pubmed
|gwas_rs|The RS# which led to the GWAS annotation match
|gwas_chr|The chromosome on which the GWAS match was found
|gwas_pos|The basepair location at which the GWAS match was found
|gwas_trait|The GWAS annotation’s associated trait or phenotype
|gwas_snps|The full list of SNPs in the GWAS association
|gwas_orbeta|The odds ratio or beta of the GWAS association
|gwas_allele95ci|The allele 95% confidence interval of the GWAS association
|gwas_riskAfreq|The risk allele frequency of the GWAS association
|gwas_pubmed|The PubMedID of the GWAS association

Inspection of Biofilter’s source code may reveal additional supported columns. They are not documented here because they are only used for internal or debugging purposes and may change or disappear in a future release; use them at your own risk.