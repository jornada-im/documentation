# Making EML with EMLassemblyline

The R package [EMLassemblyline](https://ediorg.github.io/EMLassemblyline/) can be used to make EML documents derived from the metadata stored in text files. 

## Basic setup



## Steps to create metadata (EML) file

**If updating and publishing new data (and metadata):**

  1. Download and open the latest dataset [for the template it doesn't matter, `mtcars` is built into R].
  2. Make any data file changes necessary with R in the `build_210000000.R` script [mtcars is modified in the template build script]
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
