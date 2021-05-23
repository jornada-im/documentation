# Creating & updating datasets in JRN Metabase

## Setting up a dataset directory

Jornada datasets consist of data files, metadata, and sometimes additional files. Not all of these can be stored directly in the JRN Metabase. So, each Jornada dataset should have a dedicated directory, usually on the Jornada shared drive. These directories are typically prefixed with the Jornada dataset ID number. As noted below, all data entities to be incuded with the published dataset, as well as some metadata to be attached, will be stored in this directory. It is also a good place to store scripts used to QA/QC datafiles and publish finished datasets.

## Creating or updating a dataset manually in `DataSet` tables

Metadata specific to datasets are stored in `DataSet`-prefixed tables in JRN Metabase. To create a new dataset record in JRN Metabase, new records (rows) must be added to these tables. To update an existing dataset in JRN Metabase, existing records in `DataSet` tables must be altered, and new records (rows) may be added. The `DataSet` tables are all linked by the `DataSetID` columns, so new records added to a table will require that this key be added, and updates to any dataset will need to take place in records with the correct, corresponding `DataSetID`. The `DataSetID` numbers in JRN Metabase correspond to the jornada dataset IDs we currently use for all our data packages (210001001, for example).

Before adding a new dataset to JRN Metabase, keep in mind that NOT ALL metadata for the data package will be added directly to the database. Long-form text metadata, particularly the abstract and methods for the dataset, are typically kept in the dataset directory as .docx, markdown or other text files. The filenames for the abstract and methods will be added to JRN Metabase, but not the metadata themselves.

Additionally, the data entities (CSV tables, geoTIFFs, zip archives, PDFs, etc...) that will be included with your dataset must also be in the dataset directory. You will add filenames for these to JRN Metabase, but not the files themselves.

### Order of operations

1. In the `lter_metabase` schema, open the `DataSets` table and add a new row.
2. Enter a package ID in the `DataSetID` column.
3. Populate the rest of the row with the dataset title, name of the abstract file, publication date, and other info.
4. The 'Boilerplate' column has a parent table in the `mb2eml_r` schema that identifies seldom changing metadata elements such as <intellectualRights> and <project> metadata. Choose a boilerplate value that matches your project and dataset.
5. Open the `DataSetEntities` table and enter (or update) a record for each of the data entities. Each of these records refer to a file your dataset directory that will be published as part of the dataset.
    - If the dataset has more than one entity you will enter multiple new rows and order them in the `EntitySortOrder` column. 
    - The `DataSetID` and `EntitySortOrder` columns together will be used to identify data entities in other tables, such as in the `DataSetAttributes` table (below). 
6. If your data package has tabular data entities (`DataSetEntities.EntityType`="dataTable") open the `DataSetAttributes` table to describe the columns in each tabular data entity.
    - Rows with an `EntitySortOrder` of "1" will describe the column attributes in the first dataEntity included in the dataset.
    - Additional rows with the same `DataSetID` and incremented `EntitySortOrders` (2, 3, 4...) will describe column attributes in additional data entities for the dataset.
7. If the `DataSetAttributes` table defines any colums as categorical variables by having a `MeasurementScaleDomain of "nominalEnum", you will need look at the categorical data column in the data table and add a record for each categorical variable code used to the `DataSetAttributeEnumeration` table, using codes defined in the `ListCodes` table.
8. Continue until all table are filled out... a sensible order of operations from here (will add more detail later) would be to fill out:
    - `DataSetMethod` with information about the methods file in your dataset directory, usually named "methods.<datasetID>.md" or similar.
    - `DataSetKeywords` with appropriate keywords chosen from the `ListKeywords` table.
    - `DataSetPersonnel` with relevant personnel `NameID`s chosen from the `ListPeople` table.
    - `DataSetSites` with a site identifier chosen from the `ListSites` table.
    - `DataSetTemporal` to define the time periods covered in the data.

Note that many columns have constraints set - they will need to contain values, and these may be required to come from a parent table.

### Things to remember

* Make sure there are not extra spaces entered in the database. For example, no extra spaces around the "ColumnName" entries in `DataSetAttributes` table.
* Column attributes with `nominalEnum` measurement scale types are tricky. To make them work:
    - set the `StorageType` to integer or string, depending what they are
    - shouldn't  need any `Unit`, `NumberType` or other values.
* Free text column attributes can have a `nominalText` measurement scale and a string `StorageType`.
    - shouldn't  need any `Unit`, `NumberType` or other values.
* Don't know how `ordinalEnum` and `ordinalText` measurement scales work yet. Can't find much documentation about it, though the [EML schema](https://eml.ecoinformatics.org/eml-schema.html#the-eml-attribute-module---attribute-level-information-within-dataset-entities) might define them.


