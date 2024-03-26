---
layout: default
title: Home
nav_order: 1
---

{:toc}

# What is Biofilter?
Biofilter is a software tool that provides a convenient single interface for accessing multiple publicly available human genetic data sources. These sources include information about the genomic locations of SNPs and genes, as well as relationships among genes and proteins such as interaction pairs, pathways and ontological categories. Biofilter will cross-reference all of this prior biological knowledge in several different ways, with any number of combinations of input data.


# Why use Biofilter?
While genome-wide association studies (GWAS) have been used to identify genetic variants that contribute to disease susceptibility on a single-variant single-phenotype level, other approaches can be used to investigate the association between genetic and phenotypic variation. Use of the software tool Biofilter is one such example of a complementary but alternate approach. Biofilter allows users to work with a range of types and formats of data, including SNPs, copy number variant (CNV), and gene location information, along with a repository of diverse biological knowledge distilled from multiple external databases. Via Biofilter, users can annotate data or results with relevant biological knowledge for analysis and interpretation.  Biofilter also allows users to filter data based on biological criteria, allowing users to harness information from multiple sources for the reduction of data for analysis. Finally, Biofilter can be used to generate biological-information derived pairwise interaction models for reducing the computational and statistical burden of large-scale interaction data analysis, while also providing a biological foundation to support the relevance of statistically significant results. The use of Biofilter may help to elucidate a new picture of the relationship between genetic architecture and complex phenotypic outcomes such as the presence or absence of disease. 

# Analysis Modes

Biofilter has three primary analysis modes which each make use of the available biological knowledge in slightly different ways.

## Filtering
|**Filtering**|![filtering](images/analysis-modes/filtering.png)|Given input data, Biofilter can cross-reference it using stored relationships to generate a filtered dataset of supported data types|

The most straightforward of Biofilter’s primary functions is, as the name implies, filtering. Given any combination of input data, Biofilter can cross-reference the input data using the relationships stored in the knowledge database to generate a filtered dataset of any supported type (or types).

For example, a user can provide a list of SNPs (such as those covered by a genotyping platform) and a list of genes (such as those thought to be related to a particular phenotype) and request a filtered set of SNPs. Biofilter will use LOKI’s knowledge of SNP positions and gene regions to filter the provided SNP list, removing all those that are not located within any of the provided genes.

The output data type does not necessarily have to be the same data type(s) provided as input. For example, a user can provide a list of SNPs and a list of groups and request the set of genes that match both lists. In this case, there is no input set of genes to use as a starting point so Biofilter will check all known genes found in the knowledge database. The result is a list of only the genes which include at least one of the specified SNPs, and are a part of at least one of the specified groups.

Finally, filtering is not limited to a single data type: Biofilter can also identify all of the unique combinations of data types which jointly meet the provided criteria. For example, given a list of SNPs and genes, Biofilter can produce a filtered set of SNP-gene pairs. The result is every combination of SNP and gene from the two lists where the SNP is within the gene. 


## Annotation
|**Annotation**|![annotation](images/analysis-modes/annotation.png)|As opposed to ‘Filtering’, input data can be annotated with additional properties without subsetting the original list provided|

Biofilter can also annotate any of the supported data types with respect to any of the others. Like filtering, the annotations are based on the relationships stored in the knowledge database; unlike filtering, any data which cannot be annotated as requested (such as a SNP which is not located within any gene) will still be included in the output, with the annotation columns of the output simply left blank. Put another way, the difference between filtering and annotation is that filtering does not allow any blanks.

For example, a list of SNPs can be annotated with positions to generate a new list of all the same SNPs, but with extra columns containing the chromosome and genomic position for each SNP (if any). Any SNP with multiple known positions will be repeated, and any SNP with no known position will have blanks in the added columns.

Similarly, those same SNPs can be annotated with gene information; the result is similar, except that the added column contains the name of the gene containing the SNP’s position. In this case a blank value can mean two things: either the SNP does not fall within any known gene region, or the SNP has no known position with which to search for gene regions.

Annotations can also be generated for combinations of data types, or for data types which were not provided as input. In these cases the annotation will be for the output of a filtering analysis. For example, suppose the user provides a list of SNPs and a list of groups, and then requests an annotation of genes to regions. Since no genes were provided as input, Biofilter will first perform a filtering analysis to identify all genes which contain at least one of the provided SNPs, and are also part of at least one of the provided groups. This filtered set of genes will then appear in the first column of the annotation output, followed by each gene’s genomic region (if any).


## Modeling
|**Modeling**|![modeling](images/analysis-modes/modeling.png)|In addition to cross-referencing stored relationships, combo of input data can be used to reduce the search space with pairwise interactions models and groupings|

The last of Biofilter’s primary analysis modes is a little different from filtering and annotation. In addition to simply cross-referencing any given data with the other available prior knowledge, Biofilter can also search for repeated patterns within the prior knowledge which might indicate the potential for important interactions between SNPs or genes.

The key idea behind this analysis is that any pathway, ontological category, protein family, experimental interaction, or other grouping of genes or proteins implies a relationship between each of those genes or proteins. If the same two genes appear together in more than one grouping, they’re likely to have an important biological relationship; if they appear in multiple groups from several independent sources, then they’re even more likely to be biologically related in some way. Biofilter has access to thousands of such groupings and can analyze all of them to identify the pairs of genes or SNPs appearing together in the greatest number of groupings and the widest array of original data sources. These pairs can then be tested for significance within a research dataset, avoiding the prohibitive computational and multiple-testing burden of an exhaustive pairwise analysis.

Biofilter can take any combination of input data and use it to focus the search for likely pairwise interaction models. For example, a user can provide a list of SNPs and request gene-gene models; Biofilter will then only consider models in which both genes contain at least one of the specified SNPs.

The models suggested by Biofilter are also ranked in order of likelihood, using an “implication index.” This score is simply a combination of two tallies: the number of original data sources which contained the pair, and the number of different groups among those sources. For example a score of “2-3” indicates that the model appears in three different groups, and those groups originated with two different sources.

Since the interaction models are based on genes appearing together in multiple groups, Biofilter performs all model-building analyses by first generating gene-gene models. These baseline models can then be converted into models of any type by expanding each side independently, just like in a filtering analysis. For example if the user requests SNP models, Biofilter will take each baseline gene-gene model, separately map the two genes to all applicable SNPs, and then return all possible pairings between those two sets of SNPs.

# Primary and Alternative Input Datasets

So far, the descriptions and examples of Biofilter’s various analysis modes have implied that all user input exists in a single dataset. However, Biofilter can support two independent sets of user input data. These two datasets are used for slightly different things depending on the context, and so whenever input data is provided to Biofilter, the user must specify which dataset it should be added to. For this purpose there are corresponding primary and alternate input options for each type of data: “SNP” and “ALT_SNP”, “REGION” and “ALT_REGION”, and so on.

In a filtering analysis, only the primary input dataset is used; any alternate input data has no effect.

In an annotation analysis, the primary and alternate input datasets are used separately on the two sides of the annotation. For example, if a user annotates SNPs with genes then the primary input data is used to limit which SNPs are annotated at all, while the alternate input data is used to limit which genes can be considered for annotation. Put another way, this means that if a SNP cannot be linked with the primary input data then it will not appear at all in the annotation output (even with blank annotation columns); likewise if a gene cannot be linked with the alternate input data then it will not appear as an annotation for any SNP, even if its genomic region does contain the SNP’s position.

In a modeling analysis, the primary and alternate input datasets are used similarly to annotation, with one extra option. By default, both parts of a model must match the primary input data in order for that model to be generated. If there is any alternate input data, then one of the two parts of the model must also match the alternate input. For example, the user could provide SNP list A as primary input and SNP list B as alternate input, and then request SNP models. Biofilter would then only generate SNP-SNP models in which both SNPs appear in list A, and at least one of them also appears in list B. With the ALTERNATE_MODEL_FILTERING option, the effect of the primary input is relaxed a bit so that it only applies to one part of the model, while the alternate input applies to the other. In this case, Biofilter would generate SNP-SNP models where one SNP is in list A and the other SNP is in list B.


# Identifiers
Any given gene or group might go by many different names in different contexts, and Biofilter/LOKI accommodate this. For example, a single gene (let’s say “alpha-1-B glycoprotein”) might have one ID number assigned by NCBI’s Entrez Gene database (“1”), a different identifier assigned by Ensembl (“ENSG00000121410”), another one from HGNC (“5”), plus any number of symbolic abbreviations (A1BG, A1B, ABG, GAB, HYST2477).

Just as a single gene can have more than one name, there are also names which are known to be associated with more than one gene; these names are considered ambiguous. For example, although A1B is an alias of the gene A1BG, it is also an alias of the gene SNTB1 (syntrophin, beta 1). Therefore if A1B appears in an input gene list file, Biofilter will not inherently recognize which gene the user intended to include. Likewise if A1B were to appear within the bulk biological data downloaded for LOKI, then Biofilter might not recognize which gene is actually part of some pathway.

Rather than attempting to compromise on a “one size fits all” approach to this ambiguity, Biofilter and LOKI support multiple interpretations of any ambiguity that was encountered while compiling the knowledge database. Each of these interpretations comes with a slightly different trade-off between false-positives and false-negatives, and the interpretation most appropriate to the task can be selected by the user at run-time. This is covered in greater detail in a later section, but it is important to bear in mind that ambiguity will be a part of relating and cross-referencing data across multiple independent sources. Biofilter’s results can change depending on the users choice for handling ambiguity.

