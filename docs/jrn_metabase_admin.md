# JRN Metabase - Administration

## Keeping the host and PostgreSQL up to date

The jrn-metabase is usually hosted on a server running Linux or a similar OS. In the case of Ubuntu/Debian systems, keep the OS and PostgreSQL up to date with `apt`.

## Updating LTER_core_metabase

Patches are periodically released and are available on the [migration branch]() of the [LTER GitHub repository](https://lter.github.io/LTER-core-metabase/). They are pretty easy to install (see [setup](jrn_metabase_setup.md) document) but they may or may not be needed depending on how our database has evolved. Discuss with the patch creator before installing. 

**Is there a way to export patches if we change things?**

## New users

### Administrator tasks

1. Log in to the `psql` shell either from a local terminal (`sudo -u postgres psql`) or from a remote client.

        psql -U <role name> -h <host name or IP> -p 5432 <database name>

2. Add a role for the user and assign a password:

        postgres=# CREATE ROLE <name> <OTHER OPTIONS> LOGIN;
        postgres=# ALTER USER <name> WITH ENCRYPTED PASSWORD '<password>';

3. Email the user the new role/user name and password and ask them to change their password using the instructions below.

4. Grant, or revoke, editing roles (list with `\du`) to user roles:

        postgres=# GRANT group_role TO role1, ... ;
        postgres=# REVOKE group_role FROM role1, ... ;

    In the case of LTER_core_metabase, the important roles are `read_only_user` and `read_write_user`.

### New user tasks

The administrator (site IM for now), will email you a username and password that will allow you to login to the host and the PostgreSQL server. You will need to at least change the PostgreSQL server password for your user account.

1. Open a terminal on your computer and check to see if you have the PostgreSQL client (`psql`) installed. The command below should return version info. If it doesn't you need to install `psql`.

        psql --version

2. Issue the following command from your terminal:

        psql -U <username> -h <host name or IP> -p 5432 -c "ALTER USER <username> WITH ENCRYPTED PASSWORD '<new_password>';" <database name>

    where anything in angle brackets needs to be replaced with your username, server, and database information. Don't forget to leave the single quotes around your new password, but you will leave them out when accessing the database.

3. If you also want access to the host server, login with ssh and change your password using `passwd`. You probably don't really need to though.

## Copy of the database

To copy a database, including schema and data, use:

    postgres=# CREATE DATABASE <new_database_name> WITH TEMPLATE <template_database_name>;

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

Backups using `pg_dump` can also be initiated from DBeaver or pgAdmin. 

A backup can be restored with some variation of:

    psql the_db_name < the_backup.sql


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

See here... https://stackoverflow.com/questions/16618627/pg-dump-vs-pg-dumpall-which-one-to-use-to-database-backups
