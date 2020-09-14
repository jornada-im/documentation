# JRN Metabase - Populating the database

The [LTER-core-metabase documentation](https://lter.github.io/LTER-core-metabase/) on GitHub is the primary source for understanding this process. The steps to [populate](https://lter.github.io/LTER-core-metabase/populate.html) the lter-metabase schema are summarized below, with notes on how this process is being adapted for jrn-metabase.

## Schema overview

There are 4 schemas available in LTER-core-metabase, but the `lter_metabase` schema is the primary collection of tables for describing metadata and data packages. Within this schema there are 3 primary types of tables:

1. `EML`-prefixed tables hold controlled vocabularies (CV) for elements of the EML schema or network level CVs. Things like file types and unit dictionaries go here and are often populated from network-level sources. These tables are infrequently updated.
2. `List`-prefixed tables hold controlled vocabularies more specific to the site, so could contain a subset of the LTER Network Keyword CV, site personnel, Taxa, etc. These tables are more frequently updated.
3. `DataSet`-prefixed tables contain information about specific datasets in the database. These tables are updated anytime a dataset is added or updated.

The rest of the information below is about populating these 3 types of tables 

## Populating `EML` tables

For now we are using what comes with LTER-core-metabase. There will probably be some updates to at least the `EML-UnitDictionary` table at some point.

## Populating `List` tables

These tables will need to be populated with JRN personnel, keyword thesauri, our sites (and bounding boxes), and possibly our methods documents and taxa. Only a subset of the LTER Keyword CV is currently present in the keywords list table.

## Populating `DataSet` tables

These tables need to be added to or updated to add/update JRN Datasets in the database.

These tables are all linked by the `DataSetID` columns, so new rows will need this key added, and updates to DataSets will need to take place with the correct  `DataSetID`s. These numbers correspond to the DataSet IDs we currently use for our JRN data packages (210001001, for example).

Before adding a new DataSet to the JRN MEtabase, keep in mind that NOT ALL parts of the data package can be added directly to the database. The data entities (CCSVs or other files) and the abstract and methods documents (as .docx, markdown, etc) should be kept in a folder in our usual file system. You will add path to these files in JRN Metabase, but not the files themselves (for now). Once you have a folder to refer to, the order of operations to add a new dataset to JRN MEtabase is:

1. In the `lter_metabase` schema, open the `DataSets` table and add a new row.
2. Enter a package ID in the `DataSetID` column.
3. Populate the rest of the row with the dataset title, path/name of the abstract file, the publication date, and other info. Not sure whats up with the boilerplate settings yet.
4. Open `DataSetEntities` table and enter information for the data entities. If there are more than one you will enter multiple new rows and order them in the ``EntitySortOrder` column.
5.
