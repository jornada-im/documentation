# JRN Metabase - Administration

## Keeping the host and PostgreSQL up to date

The jrn-metabase is hosted on a server called `metadatadb`, which is a virtual server on the DASH server. It runs Ubuntu 20.4. Keep it, and PostgreSQL, up to date with apt.

## Updating LTER_core_metabase

Patches are periodically released and are available on the [migration branch]() of the GitHub repository. They are pretty easy to install (see [setup](jrn_metabase_setup.md) document) but they might not be needed. 

**Is there a way to export patches if we change things?**

## Adding new users



## Backup of the database

In `psql` the `pg_dump` command probably does a database dump. Not sure how to configure this yet though.

