# JRN Metabase - Setup

## Host location and login

The jrn-metabase is hosted on a server called `metadatadb`, which is a virtual server on the DASH server, (Deb's group) for now. To login on ssh you must be on the Jornada VPN, then:

    ssh <username>@<host name or IP>

## Install and configure PostgreSQL

JRN Metabase is a PostgreSQL version 12 database. Here are the Postgres docs:

[https://www.postgresql.org/docs/12/index.html](https://www.postgresql.org/docs/12/index.html)

and here is how it gets installed on Ubuntu (by default):

[https://ubuntu.com/server/docs/databases-postgresql](https://ubuntu.com/server/docs/databases-postgresql)

We just installed the default package available in Ubuntu Server. There are several configurations to fix while logged into the host to create roles and configure remote access to databases. Some of these changes involve editing config files and some involve using the postgres administrative shell, called `psql`.

### Using `psql` - the PostgreSQL administrative shell

Once logged into the PostgreSQL host, the `psql` can be entered (as the postgres user) with:

    sudo -u postgres psql

The `postgres=#` prompt will appear indicating you have entered the shell in the `postgres` role, which is the default administrative role with superuser privileges. It has no password set on a new install, so that will need to be set according to instructions below.

Other PostgreSQL roles should be created for database users. Once these are created and remote access is configured on the host, `psql` can be run from remote clients (if installed) so that users can login to databases on the host using commands like:

    psql -U <rolename> -h <hostname or IP> -p <postgres port> <database name>

In general the postgres port is 5432.

### To add roles and configure access:

There are some recommended changes to configure remote access to a PostgreSQL installation and to create the roles suggested for LTER_core_metabase (defined [here](https://github.com/lter/LTER-core-metabase/blob/master/docs/quick_start.md#1-create-users-and-assign-privileges). This will most likely be done while logged into the host.

1. Edit `postgresql.conf` to allow remote connections (`listen_addresses` line, change 'localhost' to '\*')

        sudo vim /etc/postgresql/12/main/postgresql.conf # then make changes

2. Enter the `psql` as the default user (postgres)

        sudo -u postgres psql  # assuming logged on to host

3. Change the postgres role's password to something more secure    

        postgres=# ALTER USER postgres WITH ENCRYPTED PASSWORD '<password>';


4. Alter the PostgreSQL authentication config file to allow postgres user to authenticate with md5

        sudo vim /etc/postgresql/12/main/pg_hba.conf

    The line should look like:

        local   all         postgres            md5

5.  Add a line at the end of `pg_hba.conf` to allow remote connections. This can allow IPs 0.0.0.0/0 for now using md5 for now but ***probably should be stricter soon.*** This looks like:

        host    all     all      0.0.0.0/0      md5

6. Create a role for the database owner and set password

        postgres=# CREATE ROLE <name> CREATEDB CREATEROLE LOGIN;
        postgres=# ALTER USER <name> WITH ENCRYPTED PASSWORD '<password>';

7. Create other roles specified for LTER_core_metabase and give privileges (may need to be done after database is created):

        CREATE ROLE read_write_user
        CREATE ROLE read_only_user
        GRANT read_only_user SELECT ON lter_core_metabase
        GRANT read_write_user SELECT INSERT UPDATE ON lter_core_metabase

8. After making changes on server restart the postgres server

        sudo systemctl restart postgresql.service

## Creating databases

### First a test

There are some PostgreSQL tools available in Linux userspace, so to create a testing database do:

    createdb jrn-metabase-test

Or you can log into `psql` and do:

    CREATE DATABASE jrn-metabase-test

Then you can log in to the database (from the host):

    psql jrn-metabase-test

and issue SQL commands and queries if you want.

### Make lter_core_metabase

1. Clone the [lter_core_metabase git repository](https://github.com/lter/LTER-core-metabase)

2. Edit the 2 sql files to replace %db_owner% with the name of the database owner

3. Remove the 'TABLESPACE = pg_default' line (don't know why, but works)

4. Create the database:

        psql -f GitHub/LTER-core-metabase/sql/00_create_db.sql

5. Set up the schema with `onebigfile.sql` (this is if logged on to host).

        psql -U <db_owner username> -h localhost -d lter_core_metabase < GitHub/LTER-core-metabase/sql/onebigfile.sql

### Applying patches

There are patches created for LTER_core_metabase periodically that may add new features or fix bugs between versions. These are in the [migration branch](https://github.com/lter/LTER-core-metabase/tree/migration/sql) of the repository on GitHub. You should only apply the patches that are not already in `onebigfile.sql`, though it can be hard to tell which those are (probably best to ask Gastil et al.).

An example command to install these is:

    psql -U <username> -h <hostname> -d lter_core_metabase < 1_create_schemas_tables.sql

