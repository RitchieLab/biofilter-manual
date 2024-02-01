---
layout: default
title: Installation
nav_order: 3
---

# Installation Instructions for Biofilter 3.0

## Prerequisites
The following prerequisites are required to compile the LOKI database and run Biofilter:

* Python, version 2.7  or later, but not version 3.x
* Python module “apsw” (Another Python SQLite Wrapper)
* SQLite, version 3.6 or later

Note that the dependency on SQLite may be satisfied via the “apsw” Python module, since it often comes with an embedded copy of the necessary SQLite functionality. However, if LD Spline will be used (see below) then the SQLite development files will also be required, and these are not packaged with “apsw”. In either case, if in doubt, consult your system administrator.

## Platforms
Biofilter was developed in Python, and should therefore run on Linux, Mac OS X or Windows.

## Installing Biofilter
Biofilter can be downloaded from [www.ritchielab.org](https://ritchielab.org/software/biofilter-download-1). To install it onto your system, simply use Python to run the included “setup.py” script with the “install” option:

```
python setup.py install
```

This will place the Biofilter and LOKI files in your system’s usual place for Python-based software, which is typically alongside Python itself. The installation can also be done in a different location by using the “  prefix” or “  exec prefix” options. If you wish to use LD profiles, add the “  ldprofile” option in order to compile and install ldspline (see Appendix 2).

## Compiling Prior Knowledge
The LOKI prior knowledge database must be generated before Biofilter can be used. This is done with the “loki-build.py” script which was installed along with Biofilter. There are several options for this utility which are detailed below, but to get started, you just need “  knowledge” and “  update”:

```
loki-build.py --verbose   knowledge loki.db   update
```

This will download and process the bulk data files from all supported knowledge sources, storing the result in the file “loki.db” (which we recommend naming after the current date, such as “loki-20140521.db”). The update process may take as few as 4 hours or as many as 24 depending on the speed of your internet connection, processor and filesystem, and requires up to 30 GB of free disk space: 10-20 GB of temporary storage (“C:\TEMP” on Windows, “/tmp” on Linux, etc) plus another 5-10 GB for the final knowledge database file.

By default, the LOKI build script will delete all sources’ bulk data downloads after they have been processed. If the knowledge database will be updated frequently, it is recommended to keep these bulk files available so that any unchanged files will not need to be downloaded again. This can be accomplished with the “  archive” option.


### LOKI Build Script Options


```
--help
```
* Displays the program usage and immediately exits.

___

```
--version
```
* Displays the software versions and immediately exits. Note that LOKI is built upon SQLite, which will also report its own software versions. 

___

```
--knowledge
```
* Argument: *\<file>*
* Default: *none*
* Specifies the prior knowledge database file to use.

___

```
--archive
```
* Argument: *\<file>*
* Default: *none*
* Shorthand for specifying the same file as both the “  from archive” and “  to archive”.

___

```
--from-archive
```
* Argument: *\<file>*
* Default: *none*
* An archive of downloaded bulk data from a previous run of the LOKI build script. The bulk data files available for download from each source will be compared against those found in the archive, and only files which have changed will be downloaded. If not specified, the script will start from scratch and download everything.

___

```
--to-archive
```
* Argument: *\<file>*
* Default: *none*
* A file in which to archive the downloaded bulk data for a later run of the LOKI build script. If not specified, the script will reclaim disk space by deleting all original data after processing it.

___

```
--temp-directory
```
* Argument: *\<directory>*
* Default: *platform-dependent*
* The directory in which to unpack the “  from-archive” (if any) and then download new bulk data. If not specified, the system’s default temporary directory is used.

___

```
--list-sources
```
* Arguments: *[source][…]*
* Default: *none*
* List the specified source module loaders’ software versions and any options they accept. If no sources are specified, all available modules are listed.

___

```
--cache-only
```
* *Argument: *none*
* Causes the build script to skip checking any knowledge sources for available bulk data downloads, allowing it to function without an internet connection. Instead, only the files already available in the provided “  from-archive” file will be processed. If any source loader module is unable to find an expected file (such as if no archive was provided), that source loader will fail and no data will be updated for that source.

___

```
--update
```
* * Arguments: *[source][…]*
* Default: *all*
* Instructs the build script to process the bulk data from the specified sources and update their representation in the knowledge database. If no sources are specified, all supported sources will be updated.

___

```
--update-except
```
* Arguments: *[source][…]*
* Default: *none*
* Similar to “update” but with the opposite meaning for the specified sources: all supported sources will be updated except for the ones specified. If no sources are specified, none are excluded, and all supported sources are updated.

___

```
--option
```
* Arguments: *\<source>\<options>*	
* Default: *none*
* Passes additional options to the specified source loader module. The options string must be of the form “option1=value,option2=value” for any number of options and values. Supported options and values for each source can be shown with “list-sources”.

___

```
--force-update
```
* Argument: *none*
* The build script will normally only update from a sources if it detects that an update is necessary, either because new data files have been downloaded from the source or because the source’s loader module code has been updated. With this option, the build script will update all specified sources, even if it believes no update is necessary.

___

```
--finalize
```
* Argument: *none*
* Causes the build script to discard all intermediate data and optimize the knowledge database (after performing an “update”, if any). This reduces the knowledge database file size and greatly improves its performance, however it will no longer be possible to update the file with any new source data.

___

```
--no-optimize
```
* Argument: *none*
* Instructs the build script to skip the database optimization and compacting step that it normally performs after completing all source updates. This may be useful if you know you will be updating additional sources later, since the optimization can take some time and will have to be done again anyway after the next update.

___

```
--verbose
```
* Argument: *none*
* Prints additional informational messages to the screen; this is the default setting.

___

```
--quiet
```
* Argument: *none*
* Suppresses additional informational messages; the opposite of verbose.

___

```
--test-data
```
* Argument: *none*
* Switches the build script into test mode, in which it uses an alternate set of source loader modules. These sources do not contain actual biological knowledge; instead, they specify a minimal simulated set of knowledge which can be easily visualized and used to test and understand the functionality of LOKI and Biofilter. Knowledge database files created in test mode cannot be updated in the standard mode, and vice versa. Refer to the **Example Knowledge** section for more information.