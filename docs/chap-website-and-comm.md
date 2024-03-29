---
title: Chapter X - The JRN website and other communication platforms
author: The Jornada Information Management Team
...

# The JRN Website

The JRN website runs on Wordpress and is hosted by [WPengine](https://wpengine.com). The JRN webmaster, currently the lead Information Manager, has a WPengine.com account that allows access to the website backend for account management, website backups, and other administrative tasks. For content creation, editing, and website design, user accounts are created in the Wordpress environment. 


## Environments

There are 3 Wordpress environments available at the hosting provider.

* Production - this is the live JRN website. Only the webmaster has editing rights in this environment and is responsible for pulling in changes from the staging environment.
* Staging - in close sync with the Production environment, new content such as blog posts and pictures can be created and tested here. The project manager and other staff have editing access in this environment.
* Development - New plugins, themes, and other features are tested here and pushed to Staging when ready. This is mainly used by the webmaster or other developers.

Each environment has a unique name and web address at {environment}.wpengine.com. The production environment is the live website, so the DNS entry for lter.jornada.nmsu.edu points to this site.

## Secure file management

The hosting provider allows secure shell (ssh) access to any environment using an SSH gateway. See setup instructions [here](https://wpengine.com/support/ssh-gateway/). Once set up, issuing the command

    ssh {environment}@{environment}.ssh.wpengine.net

should put the user into a terminal session on the web server. To move a directory of files, such as one of our website's client-side apps, from a local machine to the website, use `scp`. For example, to push the zotero-biblio app to an environment you might do:

    scp -r GitHub/zotero-biblio {environment}@{environment}.ssh.wpengine.net:/sites/{environment}/

There are occasional issues with the SSH Gateway at our hosting provider, so luckily, management of files with git is also supported. See the [git setup instructions](https://wpengine.com/support/git/) 


## Managing Wordpress from the command line

Once logged in to the shell of the web server environment there are a number of Wordpress command-line utilities that are available. See the description here:

    https://developer.wordpress.org/cli/commands/

### Removing mixed content (http:// -> https://)

For example, to remove mixed comment by replacing "http://" addresses with "https://" issue this command from the shell:

    wp search-replace 'http://{environment}.wpengine.com' 'https://{environment}.wpengine.com'


## Pushing changes to JS apps (lter-datacat, pub_list)

The primary data catalog and the publication lists, including the bibliography and non-EDI data catalog, are client-side apps hosted on our website (`lter-datacat` and `Zotero-JavaScript-Search-Client`, respectively) . Placing these apps there, and updating them, means adding a folder to the website's root directory. This is done using scp, generally (see above), or by pushing changes via git.


# Website content

There is a variety of static and dynamic content on the website an described below. This point of this document is to describe how that content was developed, how it is maintained, and by whom. Important website content areas are listed on the Homepage, or under the main menu items listed below.

## Homepage

A mix of static and dynamic content.

The "Recent posts" section is a post grid populated with Wordpress posts beloging to the 'Featured' category. To add a new post to this section, create a post and assign to this category. All "Featured" posts created are collected on the [featured post history page](https://lter.jornada.nmsu.edu/featured-post-history/). This page is also where the "News" link in the main menu goes.

Still more to describe here...

## About

Pages under this menu are mostly static content that has been adapted from earlier versions of the website. Mostly this content gets updated during website development pushes during proposal writing or midterm reviews.

### Personnel

The StaffMembers plugin handles this - need to write up more on how this works...

## Research

Mostly static content - again, usually updated during proposal pushes.

## Data

This menu contains 3 data catalogs:
    
* The main LTER one
* Spatial data (static)
* The partner catalog (still in development).

These are described [below](chap-website-and-comm.md#data-catalogs). 

The [Interactive Data Viewer](https://jrnstaging.wpengine.com/data-catalog/interactive-data-viewer/) is also here (and it needs to be documented somewhere...), as is the static species lists (that also need updating...).

## Publications

There are both static and dynamic pages under the "Publications" menu. The [Bibliography page](https://lter.jornada.nmsu.edu/publications/) is showing an app called `zotero-biblio` that reads our bibliography from a database hosted at [Zotero.org](https://zotero.org).  The `Zotero-JavaScript-Search-Client` app is written in javascript and was originally developed by Tim Whiteaker at BLE LTER. Our fork of this app, which has some customizations, is hosted and maintained in [a GitHub repository](https://github.com/jornada-im/Zotero-JavaScript-Search-Client). It is running on the Bibliography page through an iframe, but the app lives at [https://lter.jornada.nmsu.edu/Zotero-JavaScript-Search-Client/complete_jrn.html](https://lter.jornada.nmsu.edu/Zotero-JavaScript-Search-Client/complete_jrn.html) on the website.

### The Zotero bibliography

#### Tagging

There is a tagging system for tracking the relationship between publications in the bibliography and the Jornada LTER program.

* `JRN funded` signifies a work that was directly funded by the Jornada LTER grant, with some acknowledgement of that in the text.
* `JRN assisted` signifies a work that was supported by the LTER program in some way that is apparent in the text - Jornada LTER data are used, assistance from particular people, or the work is supported by one of our leveraged programs - but direct funding from the JRN LTER is not acknowledged.
* `JRN related` signifies a work that is related to the JRN LTER program through location, personnel (investigator coauthor), or research theme, but has no known direct or indirect support from the program. A number of USDA-ARS works on or near the Jornada fall in this category.
* `JRN foundational` is used to tag works that were instrumental in the development of the JRN research program, but did not occur as part of the JRN LTER program, usually because they are from before the JRN LTER program was initally funded.
* `JRNStatusVerified` is a tag to indicate that someone in the program has checked the publication and verified that one of the tags above is accurately applied.

### Books

The [Books page](https://lter.jornada.nmsu.edu/jornada-books/) is a post grid populated with Wordpress posts beloging to the 'Books' category. To add new books, create a post and assign to this category.

Other pages under the "Publications" menu are static content.

## Outreach

Mostly static content but the GRFP, Short Course, and REU pages contain post grids that display posts under the "Graduate-fellowship-project", "Ecology-short-course", and "REU-project" categories, respectively.

## For Researchers

Static page - should stay fairly in-sync with [this JER page](https://jornada.nmsu.edu/ltar/data/documentation).


# Website apps

## Data catalogs

The data catalog seen at [https://lter.jornada.nmsu.edu/data-catalog/]([https://lter.jornada.nmsu.edu/data-catalog/) is JRNs primary data catalog and is a javascript app called `lter-datacat`.

### `lter-datacat`

The `lter-datacat` app is written in javascript and was originally developed by Geovany Ramirez and Jason Coombs. It is running on the main data catalog page through an iframe, but the app lives at [https://lter.jornada.nmsu.edu/lter-datacat/](https://lter.jornada.nmsu.edu/lter-datacat/) on the website.

The app is hosted and maintained in [a GitHub repository](https://github.com/jornada-im/lter-datacat)

### Partner data

This is in development on the staging website. It will list data held at repositories other than EDI.

In development, there is an instance of the Zotero-JavaScript-Search-Client tool running at [https://lter.jornada.nmsu.edu/Zotero-JavaScript-Search-Client/complete_jrndata.html](https://lter.jornada.nmsu.edu/Zotero-JavaScript-Search-Client/complete_jrndata.html) that can be i-framed in when ready.

### Spatial data

[The spatial data catalog](https://lter.jornada.nmsu.edu/data-catalog/spatial-data/) is basically just a list of layers that can be downloaded as kmz files. Nothing fancy.

## Zotero bibliography

### Maintaining the Zotero app

Once you make changes to the repository you can upload the whole thing to our webhost using scp. The proper command would be:

    scp -prq GitHub/Zotero-JavaScript-Search-Client/* {envname}@{envname}.ssh.wpengine.net:~/sites/{envname}/Zotero-JavaScript-Search-Client/

where {envname} is the environment name at the host (WPengine currently). You'd need to have some ssh keys set up.

## Interactive data viewer


# Mailing lists

## Shared NMSU Outlook mailboxes

* **jornada.data@nmsu.edu**
    - This address is displayed on the Jornada LTER and USDA websites as the contact address for data-related inquiries. It is also listed as the contact email address for metadata describing published Jornada datasets, including in most EML documents and packages at EDI or Ag Data Commons.
    - Mail recieved is forwarded (using a mailbox rule) to primary members of the Jornada IM Team and the mailbox can be accessed through the Outlook web app by those same primary IM Team members.
    - Previously a GMail account, `datamanager.jrn.lter@gmail.com`, was used for this purpose, and all mail to that address is now forwarded to the present address (`jornada.data@nmsu.edu`).

* **jornada.lter@nmsu.edu**
    - This address is displayed on the Jornada LTER websites as the contact address for program-related inquiries. Its also the contact address for the Jornada LTER Google account and an admin/moderator on the JRN-L and JRNSTUDENT-L listserv accounts.
    - Mail recieved is forwarded (using a mailbox rule) to primary Jornada LTER staff members (lead PI, program manager, information manager) and the mailbox can be accessed through the Outlook web app by those same primary staff members.
    - Previously, the program manager's personal GMail address had been listed for this purpose.

## GNU Mailman lists on lists.nmsu.edu

There are 3 lists assigned to Jornada LTER on NMSU's listserv system:

* JRN-L@lists.nmsu.edu (or JRN-L@nmsu.edu)
    - Used for general-interest email broadcasts to anyone affiliated with the Jornada LTER or voluntarily following this list. People can subscribe to this using a form on our website homepage.
* JRNSTUDENT-L@lists.nmsu.edu (or JRNSTUDENT-L@nmsu.edu)
    - Used mainly by JRN staff to contact graduate and undergraduate students, or of students to broadcast messages for themselves. Anything sent to JRN-L gets sent to this list as well (automatically, without duplicates).
* JRNSTAFF-L@lists.nmsu.edu (or JRNSTAFF-L@nmsu.edu)
    - Currently unused.

To administer these one must be on the NMSU VPN, or on campus, and visit the admin portal for each. The addresses for these are <https://lists.nmsu.edu/mailman/admin/{listname}>, where {listname} is jrn-l, jrnstudent-l, or jrnstaff-l.

### Managing the lists

We use the basic list admin tools provided by Mailman - the site IM and PM mainly handle it (and are still learning).




