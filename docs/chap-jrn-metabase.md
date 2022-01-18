---
title: JRN Metabase
author: The Jornada Information Management Team
...

# Setup

JRN Metabase (`jrn_metabase`) is a PostgreSQL database that can be stored and accessed on either a local or remote host. Generally we configure it on a remote host for multi-user access. PostgreSQL uses a client/server model, which means that the database server application (`postgres`) runs on the host system and manages the databases and all incoming connections from client applications. Users can choose from a number of client applications to connect to a server and database(s), either locally (from the server's host machine), or remotely. The standard, commandline client interface to PostgreSQL is `psql`, which can be, or already is, installed on most computers. We also use [DBeaver](https://dbeaver.io/download/) as a graphical client for `jrn_metabase`. Links to PostgreSQL and community documentation are on the [Postgres Links page](postgres_links.md).

## Host setup

For the remote host, we use an Ubuntu server running an up-to-date PostgreSQL server. To access a remote host over a terminal connection use:

    ssh <username>@<host name or IP>

Note that if the host you are accessing is a Jornada server you will need to use the Jornada VPN from outside Wooton Hall.

To install PostgreSQL, use the most current installation method for the host's operating system. We installed the default packages available in the latest version of Ubuntu Server. In Linux systems (Ubuntu, Debian, macOS, etc), installation of PostgreSQL creates a system user and a database server role that are both named `postgres`. The PostgreSQL administrative shell client, called `psql', is also installed by default. Many more details on PostgreSQL server administration setup and administration can be found in the [official PG Administrator Guide](https://www.postgresql.org/docs/current/admin.html).

### Using the `psql` client

When logged into the PostgreSQL host, any system user with sudoer privileges can switch to the `postgres` user and enter the `psql` shell with:

    sudo -u postgres psql

The `postgres=#` prompt will appear indicating you have entered the shell in the `postgres` role, which is the default administrative role with superuser privileges. It has no password set on a new install(, so that will need to be set according to instructions below.)

Other PostgreSQL roles should be created for database users. Once these are created and remote access is configured on the host, `psql` can be run from remote clients (if installed) so that users can login to databases on the host using commands like:

    psql -U <rolename> -h <hostname or IP> -p <postgres port> <database name>

In general the postgres port is 5432.

### Configuring remote access

There are several configurations to set to allow remote access to a PostgreSQL database cluster. Some of these changes involve editing config files and some involve using `psql`. Most likely you'll do this from your user account on the host machine.

1. Edit `postgresql.conf` to allow remote connections. To do this, open the file (usually in `/etc/postgresql/<version>/main`) and locate the `listen_addresses='localhost'` line, uncomment if needed, and change it to:

	listen_addresses='\*'

2. Now give the `postgres` user a password. Enter the `psql` as the default user (postgres)

        sudo -u postgres psql  # assuming logged on to host as a sudoer

    Then change the postgres role's password to something more secure    

        postgres=# ALTER USER postgres WITH ENCRYPTED PASSWORD '<password>';


3. Alter the PostgreSQL authentication config file to allow user `postgres` to authenticate with md5 when making a remote (TCP/IP) connection.

        sudo vim /etc/postgresql/12/main/pg_hba.conf

    Add a line that looks like like this:

        host   all         postgres     0.0.0.0/0       md5

    You could also restrict by database, or ip.

4.  For other users, you can add a similar line to `pg_hba.conf` beneath the one above to allow remote connections - just change `postgres` to the user name. This can allow users from any IP (0.0.0.0/0) to login using md5. You could also let all users in this way:

        host    all     all      0.0.0.0/0      md5

    But it isn't that secure.

### PostgreSQL roles for LTER_core_metabase

There are some recommended roles to add to a PostgreSQL cluster for LTER_core_metabase (defined [here](https://github.com/lter/LTER-core-metabase/blob/master/docs/quick_start.md#1-create-users-and-assign-privileges)). This will most likely be done in psql while logged into the host.

1. Create a role for the database owner and set password

        postgres=# CREATE ROLE <name> CREATEDB CREATEROLE LOGIN;
        postgres=# ALTER USER <name> WITH ENCRYPTED PASSWORD '<password>';

2. Create other roles specified for LTER_core_metabase. If you create these before creating the LTER_core_metabase in the steps below, they will be granted the correct permissions to the schema and tables.

        CREATE ROLE read_write_user;
        CREATE ROLE read_only_user;

    If for some reason permissions for these roles need correction, or a new role needs to be added, you might need to re-run the permission granting section in the database dump for LTER-core-metabase (`onebigfile.sql`), potentially after substituting in the new role name. JRN created a separate user for one of its metabases (jrn_metabase_dev) using this method.

3. After making changes on server restart the postgres server.

        sudo systemctl restart postgresql.service

## Creating databases

### First some tests of PosgreSQL

There are some PostgreSQL tools available in Linux userspace, so while logged in to the host, you can create a testing database for a user role with:

    createdb -O <username> <databasename>

Or you can log into `psql` as a particular role and do:

    username=# CREATE DATABASE <databasename>

Once this database is created you can log in to the database from the system shel (Note the uppercase -U flag to denote the user):

    psql -U <username> <databasename>

Or connect from within psql:

    username=# \c <databasename>

After logging in you can issue SQL commands and queries or use the `psql` metacommands that are prepended by a backslash and [described here](https://www.postgresql.org/docs/current/app-psql.html).

### Create an lter_core_metabase

At Jornada, we are basically using a "stock" version of LTER-core-metabase. It only takes a few minor modifications to install the source.

1. On the host machine, clone the [lter_core_metabase git repository](https://github.com/lter/LTER-core-metabase) then `cd` into the directory.

2. Edit the 2 sql files, `sql/00_create_db.sql` and `sql/onebigfile.sql`, to replace '%db_owner%' with the name of the database owner role you created. This could be done with a standard text editor or `sed`.

3. Create the database:

        sudo -u postgres psql -f GitHub/LTER-core-metabase/sql/00_create_db.sql

    If there is a locale error you may edit locales in `00_create_db.sql` to one present on your system (`C.UTF-8` worked best for JRN), and/or create a new locale for your system (`locale-gen...`).

4. Set up the schema with `onebigfile.sql` (this is if logged on to host).

        psql -U <db_owner username> -h localhost -d lter_core_metabase < GitHub/LTER-core-metabase/sql/onebigfile.sql

### Applying patches

There are patches created for LTER_core_metabase periodically that may add new features or fix bugs between versions. These are in the [migration branch](https://github.com/lter/LTER-core-metabase/tree/migration/sql) of the repository on GitHub. You should only apply the patches that are not already in `onebigfile.sql`, though one should probably verify which those are by talking to the LTER-core-metabase team first.

An example command to install these is:

    psql -U <username> -h <hostname> -d <databasename> < GitHub/LTER-core-metabase/sql/41_consolidate_missing_enumeration_codes.sql

# Administration

## Keeping the host and PostgreSQL up to date

The JRN Metabase is usually hosted on a server running Linux or a similar OS. In the case of Ubuntu/Debian systems, keep the OS and PostgreSQL up to date with `apt`. Important tasks are listed below. For full documentation of PostgreSQL server administration see the [official PG Administrator Guide](https://www.postgresql.org/docs/current/admin.html).

## Updating LTER_core_metabase

Patches are periodically released and are available on the [migration branch]() of the [LTER GitHub repository](https://lter.github.io/LTER-core-metabase/). They are pretty easy to install (see [setup](jrn_metabase_setup.md) document) but they may or may not be needed depending on how our database has evolved. Discuss with the patch creator before installing. 

**Is there a way to export patches if we change things?**

## Metabase gotchas

* PostgreSQL interprets any keyword (like functions) or identifier (table and column names) to lowercase unless they are double quoted. So, identifiers that are created as case-sensitive using double quotes will always have to be double quoted.
    - One common convention used when writing SQL is to type SQL keywords in all caps, and identifiers in lowercase with underscores (snake_case).
    - Currently LTER_core_metabase (and JRN Metabase) violates this - table names and column names are case sensitive and pretty much always need to be double quoted.
    - See [discussion here](https://stackoverflow.com/questions/2878248/postgresql-naming-conventions).

## Managing roles (database users)

### Administrator tasks to manage roles

1. Log in to the `psql` shell either from a local terminal (`sudo -u postgres psql`) or from a remote client.

        psql -U <role name> -h <host name or IP> -p 5432 <database name>

2. Add a role for the user and assign a password:

        postgres=# CREATE ROLE <name> <OTHER OPTIONS> LOGIN;
        postgres=# ALTER USER <name> WITH ENCRYPTED PASSWORD '<password>';

    Note that LOGIN roles are needed to make initial connections to a database, so normal users should have this. CREATE USER grants LOGIN automatically.

3. Email the user the new role/user name and password and ask them to change their password using the instructions below.

4. Grant or revoke the desired editing roles (list with `\du`) to user roles:

        postgres=# GRANT group_role TO role1, ... ;
        postgres=# REVOKE group_role FROM role1, ... ;

    In the case of LTER_core_metabase, the important roles are `read_only_user` and `read_write_user`.

5. To configure remote access (TCP/IP) for new users, they will need to be allowed in the `pg_hba.conf` file in some form. See the basics of this in the [setup page](jrn_metabase_setup.md) and PostgreSQL specifics for [the `pg_hba.conf` file](https://www.postgresql.org/docs/current/auth-pg-hba-conf.html).

### New user tasks

The administrator (site IM for now), will email you a username and password that will allow you to login to the host and the PostgreSQL server. You will need to at least change the PostgreSQL server password for your user account.

1. Open a terminal on your computer and check to see if you have the PostgreSQL client (`psql`) installed. The command below should return version info. If it doesn't you need to install `psql`.

        psql --version

2. Issue the following command from your terminal:

        psql -U <username> -h <host name or IP> -p 5432 -c "ALTER USER <username> WITH ENCRYPTED PASSWORD '<new_password>';" <database name>

    where anything in angle brackets needs to be replaced with your username, server, and database information. Don't forget to leave the single quotes around your new password, but you will leave them out when accessing the database.

If you don't have `psql` available and can connect to the JRN Metabase server with DBeaver, open an SQL console or SQL script window and type and execute this command:

        ALTER USER <username> WITH ENCRYPTED PASSWORD '<password>';

In either case you will need to change the password in your connection info for future logins.

### Dropping roles

User roles that no longer need access to a database or cluster should be dropped. The [DROP ROLE](https://www.postgresql.org/docs/current/sql-droprole.html) SQL command will do this, as will the [dropuser](https://www.postgresql.org/docs/current/app-dropuser.html) client application. Often a role will have ownership of database objects, so those ownerships need to be reassigned before dropping. See discussion [here](https://www.postgresql.org/docs/current/role-removal.html).

## Copy the database

To copy a database, including schema and data, use:

    postgres=# CREATE DATABASE <new_database_name> WITH TEMPLATE <template_database_name>;

More examples [here](https://www.postgresqltutorial.com/postgresql-copy-database/).

## Backup the database

To backup the database the basic steps are to dump the database to an SQL file using the `pg_dump` utility:

    pg_dump -F p the_db_name > the_backup.sql 

Some options for `pg_dump` are:

* `-F c|d|t|p` output file format (custom, directory, tar, plain text)
* `-C` include commands to create database in dump
* `-O` no owner
* `-s` schema only
* `-b` include large objects in the dump
* `-v` verbose messages
* `-f` specifies the backup file name
* `-d` specifies the database to backup
* `-U` specifies the user to use
* `-h` specifies the host

So, to create a backup to a client machine, for example, run:

    pg_dump -h <hostname> -U postgres -F p <dbname> > ./path/dbname.bak.sql

Backups using `pg_dump` can also be initiated from DBeaver or pgAdmin but not sure how yet.

A backup can be restored with some variation of:

    psql the_db_name < the_backup.sql

Basic info [here](https://www.postgresqltutorial.com/postgresql-backup-database/).

Regular database backups and backup rotation should be scheduled on the server - probably with `cron`. There are some scripts in the `lter_metabase_utils` repository that are based on [these](https://wiki.postgresql.org/wiki/Automated_Backup_on_Linux).

## Copy development JRN Metabase to production

There are 2 versions of JRN metabase - a development copy called `jrn_metabase_dev` and the production version (`jrn_metabase`). Periodically, when the development database is well tested and stable, it should be copied to the production version. There are different ways to do this, the easiest of which is probably to delete `jrn_metabase`, create a new, empty database with that name, and then restore it with a nightly backup of `jrn_metabase_dev`.

On the host shell:

    dropdb jrn_metabase

In psql:

    CREATE DATABASE jrn_metabase;

On the host shell:

    psql -U <username> -d jrn_metabase -f /home/backups/postgresql/2021-04-06-daily/jrn_metabase_dev.sql

The nightly backup will need to be unzipped first. It is also possible do it all in psql with something like:

    DROP DATABASE jrn_metabase;
    CREATE DATABASE jrn_metabase WITH TEMPLATE jrn_metabase_dev;

## Migrate a database to a new host

To copy the database to a new host the basic steps are to dump the database to an SQL file using the `pg_dump` utility (see above), then copy this file to the new host and restore with:

    psql the_new_dev_db < the_backup.sql

This can feasably all be done in one command:

    pg_dump -C -h remotehost -U remoteuser dbname | psql -h localhost -U localuser dbname

Or the SQL file can be dumped to localhost like this:

    ssh remoteuser@remotehost "pg_dump -U remoteuser dbname -h localhost -C --column-inserts" > ~/Desktop/dbname.bak.sql

See discussion [here](https://stackoverflow.com/questions/1237725/copying-postgresql-database-to-another-server)

Roles are also important - to export roles and restore them in a new cluster use:

    ssh remoteuser@remotehost "pg_dumpall --roles-only -U remoteuser -h localhost" > ~/Desktop/dbname_roles.bak.sql

See [here?](https://stackoverflow.com/questions/16618627/pg-dump-vs-pg-dumpall-which-one-to-use-to-database-backups)

# Metabase schema and data model

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


# Populating metabase

The [LTER-core-metabase documentation](https://lter.github.io/LTER-core-metabase/) on GitHub is the primary source for understanding this process. The steps to populate the lter-metabase schema are summarized below ([and in the docs](https://lter.github.io/LTER-core-metabase/populate.html)), with notes on how this process is being adapted for JRN Metabase. Note that before populating, it is worthwhile to learn a little about the LTER Metabase schema and how data are stored within it. See [notes here](metabase-schema-and-data-model) about that.

## Editing tools

In general we are using `psql`, python, or DBeaver to populate and edit our databases. DBeaver has [excellent documentation](https://dbeaver.com/docs/wiki/), but users will need to install it and set it up with their user/role and password to log into JRN Metabases. PgAdmin and other tools might be useful too.

## Populating JRN Metabase with CSV imports

Tables in JRN Metabase can be updated by importing CSV files containing metadata using `psql`, DBeaver, or python. The relevant SQL command is `COPY FROM`. Setting up incoming CSVs to match the table being copied to will help, and in LTER Metabases it will be best to start with parent tables (`DataSets' in particular?) so that foreign key rules won't be violated.

In server-side `psql` use:

    COPY persons(first_name, last_name, dob, email)
    FROM '/home/username/sampledb/persons.csv' 
    DELIMITER ',' 
    CSV HEADER;  # if there is a header in the csv

In client side `psql` use `\copy`, and be aware that not all roles will be permitted to do client-site operations. All the columns in the csv need a destination column in the database table or else an error will result. [This tutorial page](https://www.postgresqltutorial.com/import-csv-file-into-posgresql-table/) helps.

`COPY FROM` operations with CSV files can also be initiated from python using a [`psycopg`](https://www.psycopg.org/) database connection. Some python scripts and modules for this are available in the [jrn_metabase_tools](https://github.com/jornada_im/jrn_metabase_tools) repository.

In DBeaver, highlight the destination table and use the 'Import' tool, then select the CSV file to import. The tool allows you to match columns between CSV and database tables and create/ignore columns if needed. Setting up incoming CSVs to match the tables in the schema beforehand will help. Documentation [here](https://dbeaver.com/docs/wiki/Data-transfer/).

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

### Things to remember

* Make sure there are not extra spaces entered in the database. For example, no extra spaces around the "ColumnName" entries in `DataSetAttributes` table.
* Column attributes with `nominalEnum` measurement scale types are tricky. To make them work:
    - set the `StorageType` to integer or string, depending what they are
    - shouldn't  need any `Unit`, `NumberType` or other values.
* Free text column attributes can have a `nominalText` measurement scale and a string `StorageType`.
    - shouldn't  need any `Unit`, `NumberType` or other values.
* Don't know how `ordinalEnum` and `ordinalText` measurement scales work yet. Can't find much documentation about it, though the [EML schema](https://eml.ecoinformatics.org/eml-schema.html#the-eml-attribute-module---attribute-level-information-within-dataset-entities) might define them.

## Tools

* [SQL Workbench](https://www.sql-workbench.eu/index.html) can compare database schemas and data (a Java app).
* [psycopg](https://www.psycopg.org/) is a PostgreSQL database driver for python.
* [SQL Alchemy](https://www.sqlalchemy.org/) is an ORM and collection of database tools for python (it can use psycopg as a driver).
* [peewee](http://docs.peewee-orm.com/en/latest/) a lighter-weight ORM.


# Create and update datasets

## Setting up a dataset directory

Jornada datasets consist of data files, metadata, and sometimes additional files. Not all of these can be stored directly in the JRN Metabase. So, each Jornada dataset should have a dedicated directory, usually on the Jornada shared drive. These directories are typically prefixed with the Jornada dataset ID number. As noted below, all data entities to be incuded with the published dataset, as well as some metadata to be attached, will be stored in this directory. It is also a good place to store scripts used to QA/QC datafiles and publish finished datasets.

## Creating or updating a dataset manually in `DataSet` tables

Metadata specific to datasets are stored in `DataSet`-prefixed tables in JRN Metabase. To create a new dataset record in JRN Metabase, new records (rows) must be added to these tables. To update an existing dataset in JRN Metabase, existing records in `DataSet` tables must be altered, and new records (rows) may be added. The `DataSet` tables are all linked by the `DataSetID` columns, so new records added to a table will require that this key be added, and updates to any dataset will need to take place in records with the correct, corresponding `DataSetID`. The `DataSetID` numbers in JRN Metabase correspond to the jornada dataset IDs we currently use for all our data packages (210001001, for example).

Before adding a new dataset to JRN Metabase, keep in mind that NOT ALL metadata for the data package will be added directly to the database. Long-form text metadata, particularly the abstract and methods for the dataset, are typically kept in the dataset directory as .docx, markdown or other text files. The filenames for the abstract and methods will be added to JRN Metabase, but not the metadata themselves.

Additionally, the data entities (CSV tables, geoTIFFs, zip archives, PDFs, etc...) that will be included with your dataset must also be in the dataset directory. You will add filenames for these to JRN Metabase, but not the files themselves.

## Order of operations

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
7. If the `DataSetAttributes` table defines any colums as categorical variables by having a `MeasurementScaleDomain` of "nominalEnum", you will need look at the categorical data column in the data table and add a record for each categorical variable code used to the `DataSetAttributeEnumeration` table, using codes defined in the `ListCodes` table.
8. Continue until all table are filled out... a sensible order of operations from here (will add more detail later) would be to fill out:
    - `DataSetMethod` with information about the methods file in your dataset directory, usually named "methods.<datasetID>.md" or similar.
    - `DataSetKeywords` with appropriate keywords chosen from the `ListKeywords` table.
    - `DataSetPersonnel` with relevant personnel `NameID`s chosen from the `ListPeople` table.
    - `DataSetSites` with a site identifier chosen from the `ListSites` table.
    - `DataSetTemporal` to define the time periods covered in the data.

Note that many columns have constraints set - they will need to contain values, and these may be required to come from a parent table.

## Things to remember

* Make sure there are not extra spaces entered in the database. For example, no extra spaces around the "ColumnName" entries in `DataSetAttributes` table.
* Column attributes with `nominalEnum` measurement scale types are tricky. To make them work:
    - set the `StorageType` to integer or string, depending what they are
    - shouldn't  need any `Unit`, `NumberType` or other values.
* Free text column attributes can have a `nominalText` measurement scale and a string `StorageType`.
    - shouldn't  need any `Unit`, `NumberType` or other values.
* Don't know how `ordinalEnum` and `ordinalText` measurement scales work yet. Can't find much documentation about it, though the [EML schema](https://eml.ecoinformatics.org/eml-schema.html#the-eml-attribute-module---attribute-level-information-within-dataset-entities) might define them.


# Postgres links

## PostgreSQL documentation

* [Current version docs](https://www.postgresql.org/docs/current/)
    - [The tutorial (Part I)](https://www.postgresql.org/docs/current/tutorial.html) is useful for general learning.
    - [Part III, the Administration section](https://www.postgresql.org/docs/current/admin.html) covers topics like installation, server and database configuration, and managing roles.
    - [Part VI, the Reference section](https://www.postgresql.org/docs/current/reference.html) describes SQL commands and client/server tools like `initdb`, `pg_dump`, `createdb` and `psql`.


## Platform specific packages and installation

* [Ubuntu](https://ubuntu.com/server/docs/databases-postgresql) and [Ubuntu on Digital Ocean droplets](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-20-04) 
* [Debian](https://wiki.debian.org/PostgreSql)

## Tutorials

* The [4 Hour Learn PostgreSQL Tutorial](https://www.youtube.com/watch?v=qw--VYLpxG4) is excellent.
* [PostgreSQL Tutorials website](https://www.postgresqltutorial.com/)
* [GalaxQL](http://sol.gfxile.net/galaxql.html) (not specific to PostgreSQL, but fun) 
* [4 Hour Learn SQL Tutorial](https://www.youtube.com/watch?v=HXV3zeQKqGY)
* [PostgreSQL Database Administration for beginners](https://www.youtube.com/watch?v=aUfPf-clLLs)

## Tools

* [SQL Workbench](https://www.sql-workbench.eu/index.html) can compare database schemas and data (a Java app).
* [psycopg](https://www.psycopg.org/) is a PostgreSQL database driver for python.
* [SQL Alchemy](https://www.sqlalchemy.org/) is an ORM and collection of database tools for python (it can use psycopg as a driver).
* [peewee](http://docs.peewee-orm.com/en/latest/) a lighter-weight ORM.
