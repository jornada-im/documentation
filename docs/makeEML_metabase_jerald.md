# Making EML with JRN Metabase

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
