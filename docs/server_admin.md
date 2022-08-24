# Server administration

The Jornada IM system relies on some cloud servers at DigitalOcean (aka droplets). Generally these are running Ubuntu Server 20.04. Below are some tools and practices for setting up servers, networking between them, managing user access, transferring and securing data, and other administrative tasks.

## Cloud server setup tasks

Creating new services at DO is easy - it can be done with the dashboard or an API. Documentation for all DO services is [available here](https://www.digitalocean.com/docs/), and for droplets see the recommended initial setup docs [here](https://www.digitalocean.com/docs/droplets/tutorials/recommended-setup/) and [here](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04)

1. Add public key to server - usually you can do this on creation or from an admin control panel. If it needs to be done after the fact [see here](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-20-04)

2. Add a non-root user with sudo privilege and allow ssh access. More info here:

    * https://www.digitalocean.com/docs/droplets/tutorials/recommended-setup/
    * https://www.digitalocean.com/community/questions/how-to-enable-ssh-access-for-non-root-users

3. Configure hostnames (if not already done at creation).

    * https://www.digitalocean.com/community/questions/how-do-i-change-hostname

4. Install software, which varies depending on server purpose.

    * All: git, cron
    * Database hosts: PostgreSQL
    * Web servers: probably the LAMP stack and WordPress, maybe some javascript.
 

## Securing cloud servers

Droplets are behind a DO cloud firewall, but if needed, firewalls can also be set up for individual droplets with UFW. This and other tasks are described in the initial server setup docs above, or:

* [DigitalOcean droplet security tutorial](https://www.digitalocean.com/community/tutorials/recommended-security-measures-to-protect-your-servers)

Unattended updates are a good idea also. For Ubuntu install the `unnattended-upgrades` package and see setup instructions [here](https://ubuntu.com/server/docs/package-management).


## Scheduled tasks

Some server tasks, like database or website backups, should be scheduled with cron.

* [DO Ubuntu cron tutorial](https://www.digitalocean.com/community/tutorials/how-to-use-cron-to-automate-tasks-ubuntu-1804)

## NFS and CIFS mounts to remote directories

The JORNADA-NETB1 storage block (sometimes called the R drive) allows CIFS connections (or SMB).

Other:

* [Setting up NFS mounts in Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-ubuntu-20-04)
