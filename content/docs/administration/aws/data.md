---
title: "Data Import"
linkTitle: "Data Import"
weight: 50
description: >
  How to get image data into the system
---

### Adding the MouseLight Open Data

The MouseLight project at Janelia has made available the complete imagery and neuron tracing annotations for its published data sets on the [AWS Open Data Registry](https://registry.opendata.aws/janelia-mouselight/).

You can easily make these available in your own HortaCloud instance by [creating a Synchronized Folder](/docs/user_manual/synchronized_folders/) which points to `/s3data/janelia-mouselight-data`.

### Adding a single volume

A Horta Sample is an object representing a single 3D volume that can be visualized and traced with Horta.

If you already have the HortaKTX format data in your mounted S3 buckets, select **File** → **New** → **Horta Sample**, and then set "Sample Name" to `<sampleDirectoryName>` and "Path to Render Folder" as `/s3data/<bucketName>/<sampleDirectoryName>`.

Open the Data Explorer (**Window** → **Core** → **Data Explorer**) and navigate to Home, then "3D RawTile Microscope Samples", and your sample name. Right-click the sample and choose "Open in Horta". This will open the Horta Panel and then from the Horta Panel you have options to create a workspace or to open the 2D or the 3D volume viewer.

### Converting your data

If your data has not been converted into HortaKTX format, you can use the [HortaCloud Data Importer](https://github.com/JaneliaSciComp/hortacloud-importer) to convert it. This tool supports many common image formats, and can be easily extended in Python to support your favorite format.
