---
layout: default
title: LD Profiles
parent: Examples
nav_order: 4
---

* TOC
{:toc}

# LD Profiles

Each LD profile is defined by two things: a reference population whose particular LD patterns are relevant to the user’s analysis, and a threshold value to specify what the user considers “high LD.” Every LOKI knowledge database file begins with a default, unnamed LD profile which contains the canonical gene region boundaries and therefore has no reference population or LD threshold. All other LD profiles are calculated based on these original boundaries, which means whenever the LOKI knowledge database is updated, the LD profiles must also be re-calculated to incorporate any changes in the original gene region boundaries.

In order to generate LD profiles, Biofilter is distributed with a separate software tool called LD Spline.This tool can use specific LD measurements from the HapMap project to extrapolate more general LD patterns, which can in turn be used to calculate LD profiles containing adjusted gene region boundaries. More information about LD Spline is available from [Ritchie Lab](https://ritchielab.org/).

# Installing LD Spline

LD Spline is written in C and must therefore be compiled for your local computing environment before it can be used. To do this automatically as part of the Biofilter installation process, simply use the “`--ldprofile`” option:
`python setup.py install --ldprofile`

This will compile and install the “ldspline” executable, along with a few supporting scripts which automate the process of generating and storing LD profiles in LOKI.

# Generating LD Profiles
The LOKI prior knowledge database file must be generated before the LD adjustment can be done; refer to the Biofilter installation instructions for details on this procedure. Once the knowledge file is available, use the “buildPopulations.py” script to generate additional LD profiles. For example:
`buildPopulations.py --db loki.db --populations CEU,YRI --dprime 0.6,0.7 --rsquared 0.8,0.9`

This will generate 8 additional LD profiles for use in LOKI and Biofilter: four each for the CEU and YRI populations, of which two represent the LD pattern using D’ thresholds of 0.6 and 0.7 and the other two use the R2 metric with thresholds of 0.8 and 0.9. Note that building LD profiles may take quite some time; plan for at least 2 hours per population when run on a local disk, or twice that on a networked filesystem (such as GPFS). The build process also requires ~2 GB of RAM and some temporary disk space in the working directory: allow for 10 GB, plus another 10 GB per population. With the modified knowledge database file, Biofilter can then make use of the alternate gene regions via the LD_PROFILE option:
`biofilter.py --knowledge loki.db –-ld-profile CEU-RS0.80`

The “`--report-ld-profiles`” option can be used to list the LD profiles available in a LOKI database file:
`biofilter.py --knowledge withld.db –-report-ld-profiles`

```
#ldprofile      description                                             metric      value
                no LD adjustment
CEU-DP0.60      CEU population from HapMap with dprime cutoff 0.6       dprime      0.6
CEU-DP0.70      CEU population from HapMap with dprime cutoff 0.7       dprime      0.7
CEU-RS0.80      CEU population from HapMap with rsquared cutoff 0.8     rsquared    0.8
CEU-RS0.90      CEU population from HapMap with rsquared cutoff 0.9     rsquared    0.9
YRI-DP0.60      YRI population from HapMap with dprime cutoff 0.6       dprime      0.6
YRI-DP0.70      YRI population from HapMap with dprime cutoff 0.7       dprime      0.7
YRI-RS0.80      YRI population from HapMap with rsquared cutoff 0.8     rsquared    0.8
YRI-RS0.90      YRI population from HapMap with rsquared cutoff 0.9     rsquared    0.9
```
# Population Build Script Options

| **Option** | **Information** |
|---|---|
|`--help`|Displays the program usage and immediately exits.|
|`--populations`|A comma-separated list of 3-letter HapMap population identifiers (i.e. “CEU”, “JPT”, “YRI”, etc.)|
|`--rsquared`|A comma-separated list of R2 threshold values (between 0 and 1) for which to generate LD profiles.|
|`--dprime`|A comma-separated list of D’ threshold values (between 0 and 1) for which to generate LD profiles.|
|`--liftover`|The location of UCSC’s liftOver utility, which is needed to convert HapMap’s LD measurements to the current reference genome build. If omitted, liftOver must be available on the path.|
|`--ldspline`|The location of the LD Spline utility, which will be installed by the Biofilter installer if given the “--ldprofile” option. If omitted, ldspline must be available on the path.|
|`--poploader`|The location of the pop_loader helper script, which will be installed by the Biofilter installer if given the “--ldprofile” option. If omitted, pop_loader must be available on the path.|
|`--db`|The LOKI prior knowledge database file in which to generate LD-adjusted gene regions. The database must already contain the canonical gene regions.|
|`--keep-data`|Generating LD profiles requires many intermediate files such as original LD data from HapMap and extrapolated LD data from LD Spline. By default these intermediate files are deleted after use; if this option is specified, they will be left in place.|