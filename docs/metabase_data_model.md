# The data model in JRN Metabase

Including some issues.

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
