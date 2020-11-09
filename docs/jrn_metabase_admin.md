# JRN Metabase - Administration

## Keeping the host and PostgreSQL up to date

The jrn-metabase is hosted on a server called `metadatadb`, which is a virtual server on the DASH server. It runs Ubuntu 20.4. Keep it, and PostgreSQL, up to date with apt.

## Updating LTER_core_metabase

Patches are periodically released and are available on the [migration branch]() of the GitHub repository. They are pretty easy to install (see [setup](jrn_metabase_setup.md) document) but they might not be needed. 

**Is there a way to export patches if we change things?**

## New users

### Administrator tasks

1. Log in to the `psql` shell for the PostgreSQL running on the database host server.

        psql -U <role name> -h <host name or IP> -p 5432 <database name>

2. Add a role for the user and assign a password:

        postgres=# CREATE ROLE <name> <OTHER OPTIONS> LOGIN;
        postgres=# ALTER USER <name> WITH ENCRYPTED PASSWORD '<password>';

3. Email the user the new role/user name and password and ask them to change their password using the instructions below.

4. Grant, or revoke, editing roles (list with `\du`) to user roles:

        postgres=# GRANT group_role TO role1, ... ;
        postgres=# REVOKE group_role FROM role1, ... ;

### New user tasks

The administrator (site IM for now), will email you a username and password that will allow you to login to the host and the PostgreSQL server. You will need to at least change the PostgreSQL server password for your user account.

1. Open a terminal on your computer and check to see if you have the PostgreSQL client (`psql`) installed. The command below should return version info. If it doesn't you need to install `psql`.

        psql --version

2. Issue the following command from your terminal:

        psql -U <username> -h <host name or IP> -p 5432 -c "ALTER USER <username> WITH ENCRYPTED PASSWORD '<new_password>';" <database name>

    where anything in angle brackets needs to be replaced with your username, server, and database information. Don't forget to leave the single quotes around your new password, but you will leave them out when accessing the database.

3. If you also want access to the host server, login with ssh and change your password using `passwd`. You probably don't really need to though.


## Backup of the database

In `psql` the `pg_dump` command probably does a database dump. Not sure how to configure this yet though.

