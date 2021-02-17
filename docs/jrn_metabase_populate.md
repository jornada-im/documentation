# JRN Metabase - Populating the database

The [LTER-core-metabase documentation](https://lter.github.io/LTER-core-metabase/) on GitHub is the primary source for understanding this process. The steps to [populate](https://lter.github.io/LTER-core-metabase/populate.html) the lter-metabase schema are summarized below, with notes on how this process is being adapted for JRN Metabase.

## Editing tools

In general we are using `psql`, python, or DBeaver to populate and edit our databases. DBeaver has [excellent documentation](https://dbeaver.com/docs/wiki/), but users will need to install it and set it up with their user/role and password to log into JRN Metabases. PgAdmin and other tools might be useful too.

## Schema overview

There are 4 schemas available in LTER-core-metabase, but the `lter_metabase` schema is the primary collection of tables for describing metadata and data packages. Within this schema there are 3 primary types of tables:

1. `EML`-prefixed tables
    - For controlled vocabularies (CV) for elements of the EML schema, network level CVs (file types, unit dictionaries)
    - often populated from network-level sources.
    - Infrequently updated.
    - CSV imports or patches might be the best way to populate
    - For now we are using what was already in LTER_core_metabase and hopefully updates will be community determined to some extent.
2. `List`-prefixed tables
    - Controlled vocabularies more specific to the site
    - We will need to populate with JRN personnel, keyword thesauri, our sites (and bounding boxes), and possibly methods documents and taxa.
    - Only a subset of the LTER Keyword CV is currently present in the keywords list table.
    - Somewhat frequently updated
    - CSV imports or manual entry/updates to populate
3. `DataSet`-prefixed tables
    - Metadata assigned to specific datasets in the database
    - Enter/update records anytime a dataset is added to or updated in JRN Metabase.
    - Populate with CSV imports, EML using EML2MB (see notes below), or "manually" by entering or updating metadata records for a dataset (see instructions below).

## Populating JRN Metabase with CSV imports

Tables in JRN Metabase can be updated by importing CSV files containing metadata using `psql`, DBeaver, or python. The relevant SQL command is `COPY FROM`. Setting up incoming CSVs to match the table being copied to will help, and in LTER Metabases it will be best to start with parent tables (`DataSets' in particular?) so that foreign key rules won't be violated.

In server-side `psql` use:

    COPY persons(first_name, last_name, dob, email)
    FROM '/home/username/sampledb/persons.csv' 
    DELIMITER ',' 
    CSV HEADER;  # if there is a header in the csv

In client side `psql` use `\copy`. All the columns in the csv need a destination column in the database table or else an error will result. [This tutorial page](https://www.postgresqltutorial.com/import-csv-file-into-posgresql-table/) helps.

`COPY FROM` operations with CSV files can also be initiated from python using a [`psycopg`](https://www.psycopg.org/) database connection. Some python scripts and modules for this are available in the [jrn_metabase_tools](https://github.com/jornada_im/jrn_metabase_tools) repository.

In DBeaver, highlight the destination table and use the 'Import' tool, then select the CSV file to import. The tool allows you to match columns between CSV and database tables and create/ignore columns if needed. Setting up incoming CSVs to match the tables in the schema beforehand will help. Documentation [here](https://dbeaver.com/docs/wiki/Data-transfer/)

## Populate with EML

There is a tool being developed called EML2MB which might allow import of metadata using EML, but not ready for primetime yet.

## How to enter or update a dataset manually in `DataSet` tables

The `DataSet`-prefixed tables need to be added to or updated to add/update JRN Datasets in the database. These tables are all linked by the `DataSetID` columns, so new rows will need this key added, and updates to DataSets will need to take place with the correct  `DataSetID`s. These numbers correspond to the DataSet IDs we currently use for our JRN data packages (210001001, for example).

Before adding a new DataSet to the JRN Metabase, keep in mind that NOT ALL parts of the data package can be added directly to the database. The data entities (CCSVs or other files) and the abstract and methods documents (as .docx, markdown, etc) should be kept in a folder in our usual file system. You will add path to these files in JRN Metabase, but not the files themselves (for now). Once you have a folder to refer to, the order of operations to add a new dataset to JRN MEtabase is:

1. In the `lter_metabase` schema, open the `DataSets` table and add a new row.
2. Enter a package ID in the `DataSetID` column.
3. Populate the rest of the row with the dataset title, path/name of the abstract file, the publication date, and other info.
4. The 'Boilerplate' column has a parent table in the `mb2eml_r` schema that identifies seldom changing metadata elements such as <intellectualRights>.
5. Open the `DataSetEntities` table and enter information for the data entities. If there are more than one you will enter multiple new rows and order them in the ``EntitySortOrder` column. The `DataSetID` and `EntitySortOrder` columns together identify <dataEntities> in other tables, such as for "DataSetAttributes." 
6. Edit the "DataSetAttributes" table
    - Rows containing a `DataSetID` and `EntitySortOrder` of one will be for describing the attribues in the first dataEntity included in the dataset.
    - Additional rows with the same `DataSetID` and incremented `EntitySortOrders` (2, 3, 4...) will be used for attributes describing columns in additional dataEntities.
7. Continue until all table are filled out...
8. Note that many columns have constraints set - they will need to contain values, and these may be required to come from a parent table.

### Things to remember

* Make sure there are not extra spaces entered in the database. For example, no extra spaces around the "ColumnName" entries in `DataSetAttributes` table.
* Column attributes with `nominalEnum` measurement scale types are tricky. To make them work:
    - set the `StorageType` to integer or string, depending what they are
    - shouldn't  need any `Unit`, `NumberType` or other values.
* Free text column attributes can have a `nominalText` measurement scale and a string `StorageType`.
    - shouldn't  need any `Unit`, `NumberType` or other values.
* Don't know how `ordinalEnum` and `ordinalText` measurement scales work yet. Can't find much documentation about it, though the [EML schema](https://eml.ecoinformatics.org/eml-schema.html#the-eml-attribute-module---attribute-level-information-within-dataset-entities) might define them.

## Tools

[SQL Workbench](https://www.sql-workbench.eu/index.html) can compare database schemas and data (a Java app).
