# Content and apps on the JRN website

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

These are described [on their own page](website_data_catalogs.md). 

The [Interactive Data Viewer](https://jrnstaging.wpengine.com/data-catalog/interactive-data-viewer/) is also here (and it needs to be documented somewhere...), as is the static species lists (that also need updating...).

## Publications

There are both static and dynamic pages under the "Publications" menu. The [Bibliography page](https://lter.jornada.nmsu.edu/publications/) is showing an app called `zotero-biblio` that reads our bibliography from a database hosted at [Zotero.org](https://zotero.org).  The `zotero-biblio` app is written in javascript and was originally developed by Tim Whiteaker at BLE LTER. Our version of the app, which might be a little different, is hosted and maintained in [a GitHub repository](https://github.com/jornada-im/zotero-biblio). It is running on the Bibliography page through an iframe, but the app lives at [https://lter.jornada.nmsu.edu/zotero-biblio/](https://lter.jornada.nmsu.edu/zotero-biblio/) on the website.

### Maintaining the Zotero app

Once you make changes to the repository you can upload the whole thing to our webhost using scp. The proper command would be:

    scp -prq GitHub/zotero-biblio/* {envname}@{envname}.ssh.wpengine.net:~/sites/{envname}/zotero-biblio/

where {envname} is the environment name at the host (WPengine currently). You'd need to have some ssh keys set up.

### Books

The [Books page](https://lter.jornada.nmsu.edu/jornada-books/) is a post grid populated with Wordpress posts beloging to the 'Books' category. To add new books, create a post and assign to this category.

Other pages under the "Publications" menu are static content.

## Outreach

Mostly static content but the GRFP, Short Course, and REU pages contain post grids that display posts under the "Graduate-fellowship-project", "Ecology-short-course", and "REU-project" categories, respectively.

## For Researchers

Static page - should stay fairly in-sync with [this JER page](https://jornada.nmsu.edu/ltar/data/documentation).


