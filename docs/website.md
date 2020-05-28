# Maintaining the JRN Website

The JRN website runs on Wordpress and is hosted by WPengine.com. The JRN webmaster, currently the lead Information Manager, has a WPengine.com account that allows access to the website backend for account management, website backups, and other administrative tasks. For content creation, editing, and website design, user accounts are created in the Wordpress environment. 

## Environments

There are 3 Wordpress environments available at the hosting provider.

* Production - this is the live JRN website. Only the webmaster has editing rights in this environment and is responsible for pulling in changes from the staging environment.
* Staging - in close sync with the Production environment, new content such as blog posts and pictures can be created and tested here. The project manager and other staff have editing access in this environment.
* Development - New plugins, themes, and other features are tested here and pushed to Staging when ready. This is mainly used by the webmaster or other developers.

Each environment has a unique name and web address at {environment}.wpengine.com. The production environment is the live website, so the DNS entry for lter.jornada.nmsu.edu points to this site.

## Secure file management

The hosting provider allows secure shell (ssh) access to any environment using the command

    ssh {environment}@{environment}.ssh.wpengine.net

To move a directory of files, such as one of our js apps, from a local machine to the website, use scp. For example, to push the zotero-biblio app to an environment do:

    scp -r GitHub/zotero-biblio {environment}@{environment}.ssh.wpengine.net:/sites/{environment}/

## Pushing changes to JS apps (data_cat, pub_list)


## Removing mixed content (http:// -> https://)

From the hosting provider shell issue:

    wp search-replace 'http://{environment}.wpengine.com' 'https://{environment}.wpengine.com'


