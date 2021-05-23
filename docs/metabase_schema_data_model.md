# JRN Metabase schema and data model

Including some issues.

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

## Attributes (DataSetAttributes and DataSetAttributeEnumeration tables)

Storage type refers to the data type in which attribute values are stored in EML (See EMLStorageTypes table). We use integer, float, string, date, and boolean types for our data.

In some ways storageType corresponds to MeasurementScaleDomainIDs

* Categorical attributes (
    - nominalEnum: un-ordered categorical stored as a string or integer storageType
    - ordinalEnum: ordered categorical, usually stored as an integer
* Numeric attributes (
    - interval: un-ordered categorical stored as a string or integer storageType
    - ratio: ordered categorical, usually stored as an integer

Stuff on the internet about this:

* https://gradcoach.com/nominal-ordinal-interval-ratio/
* https://www.statology.org/levels-of-measurement-nominal-ordinal-interval-and-ratio/

## Some thorny issues

* Personnel
    - The contact person/org is assigned in the boilerplate table. Can there be more than one contact listed for a data package?
    - Is there any way to place a single person in multiple roles for a data package? The schema seems to prevent this now.
    - What about associated parties - how do they work between lter metabase and EDI?
    - For now there is a generic contact for every data package, and John is not listed as contact for anything that he was previously listed in.

* DataSetTemporal table
    - I'm not sure how to populate this yet.
    - It needs to change as we update data packages and there doesn't seem to be a way to automatically update it. Maybe part of our R scripts?

* DataSetSites
    - Needs to be populated but not sure what the best way is.

* There doesn't seem to be an easy way to tie geographicdescription to attributes.

* CodeID - we can list all codes used in datafiles in the ListCodes table, then reference in DatasetAttributeEnumeration. But should we?
