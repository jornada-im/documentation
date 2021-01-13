---
title: Jornada Basin LTER Information Management 2020 Annual Report (DRAFT)
date: 13 January 2021
author: Gregory E. Maurer with contributions from the IM Team
...

# Introduction

This document summarizes the accomplishments of the Jornada Basin LTER (JRN) Information Management (IM) team in 2020. Most of the activity and future initiatives described here are guided by what the IM team wrote in the [Data Management Plan](https://jornada-im.github.io/documentation/JRN_LTER_data_management_plan.v3.html) submitted with our 2020 LTER proposal. This fall we received word that the LTER proposal was accepted for funding beginning in early 2021. The Data Managment plan recieved generally favorable feedback from the NSF review panel.

# Data publishing progress

There were a number of notable accomplishments in packaging and publishing JRN data products in 2020. All of these involved significant additions to, or revision of, Jornada metadata.

* We finished updating "Core/Signature" data packages in early March 2020 (except for 2) in time for submission of our LTER proposal.
* John and Geovany added 107 new data packages to EDI for Cross-Scale project meteorology data.
* 10 new non-meteorology data packages were added to EDI.
* We updated half the JRN "non-core" (older, completed) data packages on EDI.
* Two Jornada eddy covariance towers joined the AmeriFlux network and are, or will be, publishing 30 minute fluxes with the AmeriFlux data repository (Vivoni group's [Tromble tower](http://ameriflux.lbl.gov/sites/siteinfo/US-Jo2), and Systems Ecology Lab [Bajada site](http://ameriflux.lbl.gov/sites/siteinfo/US-Jo1)).
* Savannah lab UAV data (4 new packages) is staged, and we are working towards publishing several other complex "non-tabular" datasets to EDI.

**Please see further statistics and figures on our activity at the end of this document.**

# New JRN IM features, infrastructure, and data products

We improved or added to several elements of the Jornada Basin LTER Information Management infrastructure, including a new website and some IM-focused databases.

## JRN Websites

* New [JRN website](https://lter.jornada.nmsu.edu) began service June 2020.
* New [partner data catalog](https://lter.jornada.nmsu.edu/partner-data/) on the website.
* New [interactive data viewer](https://lter.jornada.nmsu.edu/interactive-data-viewer/) on the website (Geovany).
* Updates to the [Jornada IM website](https://jornada-im.github.com), including documentation, metadata templates, IM diagnostics, and data acces/analysis tools.

## Metadata database

* JRN-metabase, a PostgreSQL database following the LTER-core-metabase schema, is running on the DASH server.
* Greg and Shelly (and formerly Haneen) are 2/3 of the way to populating the database with metadata for all JRN packages.
* We have successfully made valid EML (XML metadata documents) from the database.
* We expect JRN-metabase (and associated R code) to be our production metadata system by March 2021.

## Harmonizing Jornada data

* Greg began a project to assemble and harmonize Jornada NPP and related data (in early stages still).
* Darren, and Deb's group are working on Jornada precipitation data (I don't have a lot of info on this yet).
* The IM team has identified several potential harmonized datasets to add to the interactive data viewer beginning in 2021.

# Outreach, education, and LTER Network Activity

* The Non-tabular working group (LTER/EDI, Greg is a member) concluded a draft of its "Best practices for non-tabular data archiving" document.
* Greg joined the LTER network's IM Executive committee.
* Greg joined the LTER-core-metabase and Southwest IM data management/science training working groups.


# Future initiatives

The JRN IM system has become more robust and effective over the past 12 months, approaching our goal that information management at JRN must meet or exceed all LTER Network standards and expectations. Planned activities and improvements during 2021 include:

* We have applied to host an EDI fellow in the summer of 2021 (details [here](https://environmentaldatainitiative.org/summer-fellowship-program/)). If accepted Jornada's fellow will develop workflows to publish and improve access to our non-tabular datasets, including UAV images, phenocam imagery, an derived spatial datasets.
* Greg will be getting certified as a [Carpentries](https://carpentries.org/) instructor in 2021 with the goal of bringing some data management and data science workshops to the Jornada. This will be funded by the [NM EpsCOR](https://www.nmepscor.org/what-we-do/programs#collapse-accordion-92-4) program.
* The IM team would like to expand outreach to Jornada researchers to train them in accessing and using Jornada data. There are several initiatives in development for this.
    - Data Bounty Hunters
    - Improving the data management activities at the 2021 Ecology Short Course
    - Video tutorials on the JRN website (such as using the interactive data viewer)
    - A new 1-2 day workshop on data management & data science (coordinated with MCM, SEV, and NWT LTER IMs)
* There is a significant amount of work we can do on the interactive data viewer. We gathered input from numerous PIs this fall and have discussed the mechanics of how to move forward. Greg and Geovany will be coordinating this.
* There is an initiative in the LTER network to publish site meteorology data at CUAHSI HydroServer. We will need to follow suit in some fashion, but most likely with limited subset of our met data (GIBPE 15min data only at first). Greg is working on this (slowly).

# Challenges to overcome

Below are a few challenges the IM team faces in 2021. Any ideas on how we might overcome some of them are welcome.

## Project-level metadata and spatial data issues

There are some workflows for creating, organizing, and tracking projects and their location data at the Jornada. For instance, John Anderson's "Research notification" system tracks approved research projects and should, in theory, initiate collection of metadata for each project. However, research projects commonly suffer from a lack of metadata collection and accessibility after the approval phase, and information gathered during the approval phase is not widely accessible to Jornada researchers or the public. Some things we need:

1. Project metadata database (what experiments are happening or have happened, who is in charge, what are the questions, measurements, infrastructure) accessible to John and other project managers.
2. Reference spatial data we can link to our project IDs (plot locations, sensor locations, and other coordinates telling us where experiments happened).
3. A collection of ancillary spatial data (roads, fencelines, vegetation, geomorphology). We have some of this, but its not very complete and there are multiple copies of everything.

There is a meeting planned in January with USDA, Landscape Data Commons, and others to begin addressing some of these issues in a coordinated way.

## Outreach initiatives

* There is no shared communication platform for messaging among Jornada researchers, i.e., no listserv or similar mailing list platform. This tends to fragment Jornada communication and makes cohesive messaging for JRN community activities a challenge. Some of our future initiatives, such as Data Bounty Hunters, would greatly benefit from such a platform. We need either infrastructure or budget to create a communication platform.

* I hate to use LTER budget for non-research activity, but we need some funds to make Jornada T-Shirts, hats, or stickers that could be used to encourage/reward participation in Jornada outreach initiatives (Data Bounty Hunters, for instance again).

## Infrastructure

Currently the server infrastructure at the USDA Jornada is out of date and appears to be undergoing a long transition to a more stable and supportable system. The IM team has not had any success provisioning a virtual server on this system. The IM Team *really needs a server* to host databases, web services, a communication platform (see above) and potentially a future version of our website. Since there are existing sysadmins for the USDA Jornada infrastructure, having USDA share that resource with LTER would be the most efficient & cost-effective solution, if available. Alternatively, it is possible we can do these things using cloud providers (AWS, Google cloud services), but these will require some LTER budget. Any thoughts about server options at Jornada or partner institutions are welcome.


# Tables and figures

Table 1: Data packaging updates for all, and non-meteorology JRN data packages in 2020.

+------------------------+---------+-----------+
|                        |   Total |   Non-Met |
+========================+=========+===========+
| New packages created   |     127 |        10 |
+------------------------+---------+-----------+
| Package updates        |    2159 |        62 |
+------------------------+---------+-----------+
| Unique package updates |     270 |        47 |
+------------------------+---------+-----------+

![A plot of JRN’s PASTA+ database activity (knb-lter-jrn scope), including package creation and recurring updates, at the EDI repository in 2020. Top panel: Daily actions for all packages (spikes = meteorological data package creation/updates). Bottom panel: Cumulative actions for all data packages.](/Users/gmaurer/GD_gmaurer.jrn.lter/IM/figures/JRN_EDI_2020_ann_rpt_20201220.png)

![JRN's PASTA+ database activity, as in Fig 1, but excluding the meteorology data packages John and Geovany update. Top panel: Daily actions Bottom panel: Cumulative actions.](/Users/gmaurer/GD_gmaurer.jrn.lter/IM/figures/JRN_EDI_2020_ann_rpt_NoMet_20201220.png)

![Number of \"Core\", \"Non-core\", and \"New\" data packages in stages of progress through the JRN IM system, as tracked on our Trello boards.](/Users/gmaurer/GD_gmaurer.jrn.lter/IM/figures/JRN_category_progress20201220.png)

