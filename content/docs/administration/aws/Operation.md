---
title: "Operation"
linkTitle: "Operation"
weight: 100
description: >
  Day-to-day operation of the system
---

## Importing imagery

### Single Sample Import

If you already have the data on the S3 buckets in the Workstation, select **File** → **New** → **Tiled Microscope Sample**, and then set "Sample Name" to `<sampleDirectoryName>` and "Path to Render Folder" as `/<bucketName>/<sampleDirectoryName>`.

Open the Data Explorer (**Window** → **Core** → **Data Explorer**) and navigate to Home, then "3D RawTile Microscope Samples", and your sample name. Right-click the sample and choose "Open in Horta". This will open the Horta Panel and then from the Horta Panel you have options to create a workspace or to open the 2D or the 3D volume viewer.

### Batch Sample Import

The workstation offers an option to import all your samples from an S3 Bucket mapped on the backend into the workstation. This can be done from the Workstation, select **Services** → **Load Horta Data**. This opens a [dialog box](docs/images/horta_data_import.png) that allows you to enter the location of your samples and the data owner. The location of your data can be entered like `<s3_bucket_name>/<full_moouselight_data_prefix>`, e.g. `janelia-mouselight-imagery/images`. For sample owner typically select `Mouselight Users (mouselight)` from the drop down selection. Once you click OK the system will start a background task that will try to create new samples from all folders at the specified location and you will start seeing new samples under `Home (mouselight)/3D Tile Microscope Samples` as they are imported.

## System backup

The system can be configured to run nightly backups. All it is needed is to specify a writeable bucket (HORTA_BACKUP_BUCKET) and a prefix (HORTA_BACKUP_FOLDER) that will hold the backups. Currently we only backup the Mongo database that contains samples info and tracing data and each backup will be stored in a timestamp ('yyyyMMddHHmmss') folder under the base backup prefix.

## System restore

If system backups are available the sample and tracing data can be restored from a specified backup bucket (HORTA_RESTORE_BUCKET) and prefix (HORTA_RESTORE_FOLDER) which typically was created by the nightly backup job.
