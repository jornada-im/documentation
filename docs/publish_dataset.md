# Publishing Jornada datasets


## Overview

The steps to publish a dataset:

1. Standardize and QA/QC a data file until it is in a publishable state
2. Assemble and store all necessary metadata to describe the data
3. Make a *valid* EML file (or other standardized metadata entry)
4. Publish the dataset to a repository (like EDI)

Step 1 we generally do with R. Step 2 happens in JRN_metabase. Steps 3 & 4 happen back in R with the `jerald` package.

## Some useful tools

* You can use a free, [online XML schema validator](https://www.freeformatter.com/xml-validator-xsd.html) to validate your EML. Or you can use Oxygen, [XML Notepad](https://microsoft.github.io/XmlNotepad/), [XML Copy Editor](), etc.
