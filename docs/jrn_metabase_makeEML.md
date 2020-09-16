# Making EML with JRN Metabase

At the moment we use an R package called [MetaEgress](https://ble-lter.github.io/MetaEgress/) to make EML documents derived from the metadata in our JRN Metabase. They have some [example workflows](https://ble-lter.github.io/MetaEgress/articles/usage_example.html) and [R scripts](https://github.com/BLE-LTER/MetaEgress/blob/master/example/example_workflow.R) in their GitHub repository.

## Basic setup

Install `MetaEgress` in R

Probably.... For each dataset in JRN Metabase there should be a directory and in this directory will be a `build_<datasetID>.R file. This file will call MetaEgress.

But maybe there are other ways to do it....
