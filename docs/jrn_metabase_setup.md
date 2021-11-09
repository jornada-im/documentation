# JRN Metabase - Setup

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
