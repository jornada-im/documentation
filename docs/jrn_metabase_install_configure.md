# JRN Metabase - Installation and configuration

## Host location and login

The jrn-metabase is hosted on a server called `metadatadb`, which is a virtual server on the DASH server, (Deb's group) for now. To login on ssh you must be on the Jornada VPN, then:

    ssh <username>@<server IP>

## Install and config of database

JRN Metabase is a PostgreSQL version 12 database. Here are the Postgres docs:

https://www.postgresql.org/docs/12/index.html

and here is how it gets installed on Ubuntu (by default):

https://ubuntu.com/server/docs/databases-postgresql

There are several configurations to fix while logged into the host to create roles, users, and make the database accessible remotely. Some of these are done in the config files, some in bash, and some in the postgres administrative shell, called `psql`. These are the config changes I made while logged into the host:

### To add roles and configure access:

First enter the `psql` as the default user (postgres).:

    sudo -u postgres psql

then create 3 roles, as defined [here](https://github.com/lter/LTER-core-metabase/blob/master/docs/quick_start.md#1-create-users-and-assign-privileges). The commands to issue, which are laid out in the PostgreSQL documentation, chapter 21, are:

1. Alter postgres admin role password:
    postgres=# ALTER USER postgres WITH ENCRYPTED PASSWORD '<password>';

2. Alter the authentication config file to allow postgres user to authenticate with md5
    sudo vim /etc/postgresql/12/main/pg_hba.conf

    The line should look like:

        local   all         postgres                          md5

3. Set DB owner (and create password)
    postgres=# CREATE ROLE <name> CREATEDB CREATEROLE LOGIN;
    postgres=# ALTER USER <name> WITH ENCRYPTED PASSWORD '<password>';

4. Create the roles you need:
    CREATE ROLE read_write_user
    CREATE ROLE read_only_user
    GRANT read_only_user SELECT ON lter_core_metabase
    GRANT read_write_user SELECT INSERT UPDATE ON lter_core_metabase


5.  edit `postgresql.conf` to allow remote connections (`listen_addresses` line)

6.  Add a line to allow remote connections to `pg_hba.conf`. This can allow IPs 0.0.0.0/0 for now using md5(?) for now but probably should be stricter soon.

When you log onto the server there is also a user called postgres. To become that user:

    su - postgres

then you can enter the posgres admin shell with `psql`.

## Testing database


To create a testing database:

    createdb jrn-metabase-test

OR in psql:

    CREATE DATABASE jrn-metabase-test

Then to login to the database (from the host):

    psql jrn-metabase-test

## Make the database

1. Clone the git repository

2. Edit the 2 sql files to replace %db_owner% to the username of the database owner

3. Remove the 'TABLESPACE = pg_default' line (don't know why, but works)

4. run `psql -f GitHub/LTER-core-metabase/sql/00_create_db.sql` to create the database.

5. run `psql -U <db_owner username> -h localhost -d lter_core_metabase < GitHub/LTER-core-metabase/sql/onebigfile.sql` to set up the schema

