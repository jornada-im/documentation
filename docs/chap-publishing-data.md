---
title: Publishing datasets
author: The Jornada Information Management Team
...

# Introduction

## Overview

The steps to publish a dataset:

1. Standardize and QA/QC a data file until it is in a publishable state
2. Assemble and store all necessary metadata to describe the data
3. Make a *valid* EML file (or other standardized metadata entry)
4. Publish the dataset to a repository (like EDI)

Step 1 we generally do with R. Step 2 happens in JRN_metabase. Steps 3 & 4 happen back in R with the `jerald` package.

## Some useful tools

* You can use a free, [online XML schema validator](https://www.freeformatter.com/xml-validator-xsd.html) to validate your EML. Or you can use Oxygen, [XML Notepad](https://microsoft.github.io/XmlNotepad/), [XML Copy Editor](), etc.


# Make EML with JRN Metabase and jerald

At the moment we use an R package called [MetaEgress](https://ble-lter.github.io/MetaEgress/) to make EML documents derived from the metadata in our JRN Metabase. They have some [example workflows](https://ble-lter.github.io/MetaEgress/articles/usage_example.html) and [R scripts](https://github.com/BLE-LTER/MetaEgress/blob/master/example/example_workflow.R) in their GitHub repository.

We are developing a new R package called `jerald` (which relies on `MetaEgress`, among other things) to handle much of this job for the Jornada IM team.

## Set up the R environment

Install [MetaEgress](https://ble-lter.github.io/MetaEgress/).

If you want to be adventurous, try installing [jerald](https://github.com/jornada-im/jerald).

## Set up a dataset directory
 
Jornada datasets consist of data files, metadata, and sometimes additional files. Not all of these can be stored directly in the JRN Metabase. So, each Jornada dataset should have a dedicated directory, usually on the Jornada shared drive. These directories are typically prefixed with the Jornada dataset ID number. As noted below, all data entities to be incuded with the published dataset, as well as some metadata to be attached, will be stored in this directory. It is also a good place to store scripts used to QA/QC datafiles and publish finished datasets.

A template dataset directory for data packages that rely on JRN Metabase can be found in the [jrn-metabase-utils](https://github.com/jornada-im/jrn-metabase-utils) repository. It contains all the directories and template scripts described below. This repository also contains a python script (`init_jerald_datasetdir.py`) that will build a dataset directory for you.

### Metadata sources

Incoming metadata templates may be added to the `metadata_docs/` or 
`source_data/` directories, whichever is more convenient. Older metadata 
files used for reference, such as "dsd" and "prj" files from Jornada archives, should be kept in `metadata_docs/`. 

The abstract and methods text files referred to in JRN Metabase (`abstract.210000000.md` and `methods.210000000.md`) should be kept in the top level of the dataset directory and can be updated there. With the exception of these two pieces of metadata, JRN Metabase is the primary store of metadata for Jornada datasets, and all changes should be made there.

### Data table sources

Incoming raw data, including submissions from researchers, can be kept in
the `source_data/` directory.

### Other entities

Other entities should probably be kept in the top level of the dataset directory, but me may find a better way.

### Build scripts

Build scripts here prepare the data and metadata for publication using various R packages. The resulting EML dataset may be published using the scripts, or manually using the [EDI portal](https://portal-s.edirepository.org).

* `build_210000000_dataset.R` - Basic build script that formats & QA/QCs data in `source_data/` and writes publishable data files to the working directory (`./`)
* `build_210000000_eml.R` - Creates an eml file and optionally uploads data
entities and pushes the package to EDI using APIs.
* `build_210000000_eml_jerald.R` - same as above, but uses the [jerald](https://github.com/jornada-im/jerald) R package.

## Build EML and publish

For now the `build_210000000_eml.R` template R script should have pretty good step-by-step information, but we'll document this further soon...


# Make EML with EMLassemblyline

The R package [EMLassemblyline](https://ediorg.github.io/EMLassemblyline/) (or `EAL`) can be used to make EML documents derived from the metadata stored in text files. We typically would use this process for datasets that are not otherwise in our data management system or JRN Metabase.

## Set up the R environment

Install [EMLassemblyline](https://ediorg.github.io/EMLassemblyline/).

## Set up a dataset directory
 
Jornada datasets consist of data files, metadata, and sometimes additional files, so it is helpful to create dataset directories with a consistent structure to mange these files. Jornada dataset directories are typically prefixed with the Jornada dataset ID number. As noted below, all data entities to be incuded with the published dataset, all metadata for the EML document, and the scripts used to QA/QC datafiles and make EML documents should go in this directory. In the [`EAL` documentation](https://ediorg.github.io/EMLassemblyline/) there is a dataset directory structure suggested, which we have adopted here.

A template dataset directory for data packages that rely on EAL  can be found in the [jrn-emlassemblyline](https://github.com/jornada-im/jrn-emlassemblyline) repository. It contains all the directories and template scripts described below. This repository also contains a python script (`init_eal_datasetdir.py`) that will build a dataset directory for you.

### Metadata sources

Metadata should be added to the templates in the `metadata_templates/` directory. These templates are described in the EAL documentation and there are templating functions that can be used to generate empty templates that are easy to fill in. Old metadata files used for reference, such as "dsd" and "prj" files from Jornada archives, can be kept in the top level dataset directory, or a subdirectory of your choice. 

### Data table sources

Incoming raw data, including submissions from researchers, can be kept in
the top level of the dataset directory. Once they are QA/QC'd and re-formatted for publication, the resulting data tables will be added to the `data_entities/` subdirectory.

### Other entities

Other entities, which are generally not processed or altered in any way before publication, can be put directly into the `data_entities/` directory. However, there may be some cases where the incoming otherEntity file needs some alteration and it makes sense to keep a raw version separate from a processed version. Use your discretion.

### Build scripts

Build scripts call `EAL` (and related R packages) to prepare the data and metadata for publication. Once complete, the resulting EML and data entities may be published manually using the [EDI portal](https://portal-s.edirepository.org). Metadata such as the title and entity descriptions are also in these build scripts.

* `build_<datasetID>.R` - Basic build script that modifies data and writes EML for the dataset identified by <datasetID>. 

## Build EML and publish

For now the `build_210000000.R` template R script should have pretty good step-by-step information, but we'll document this further soon...

### DRAFT Steps to create metadata (EML) file

The example package with dataset ID 210000000 is used here.

**If updating and publishing new data (and metadata):**

  1. Download and open the latest dataset.
  2. Make any data file changes necessary with R in the `build_210000000.R` script
  3. Increment `package.id` version number in build_210000000.R (`make_eml()` call)
  4. Increment the `temporal.coverage` array in build_210000000.R (`make_eml()` call)
  5. Update metadata templates.
  6. Run the build_210000000.R script in R.
  7. Publish to EDI staging server.

**If you are updating the package metadata only:**

  1. Edit the metadata templates in `./metadata_templates`
  2. Increment `package.id` version number in build_210000000.R (`make_eml()` call)
  3. Run the build_210000000.R script in R (careful not to regenerate blank templates)
  4. Publish to EDI or EDI Staging ("Evaluate/Upload Data Packages" tool)

