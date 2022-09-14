---
title: "Backup & Restore"
linkTitle: "Backup & Restore"
weight: 30
description: >
  How to backup your HortaCloud data
---

## System backup

The system can be configured to take nightly backups. All it is needed is to specify a writeable bucket (`HORTA_BACKUP_BUCKET`) that will hold the backups. You can also specify the base prefix for the backups using `HORTA_BACKUP_FOLDER`. If this is not specified the prefix will default to "/hortacloud/backups".

Currently the backup contains the Cognito users and the Mongo database (user-generated metadata and tracing data). Each backup will be stored in a timestamp (with format "yyyyMMddHHmmss") folder under the base backup prefix. The location of the backups will be `s3://<backup_bucket>/<backup_folder_prefix>/<timestamp>/jacs` for the database and `s3://<backup_bucket>/<backup_folder_prefix>/<timestamp>/cognito` for cognito users.

The backup is configured as a cron job that runs daily at 3 AM local time on the EC2 host on which the backend services run.

## System restore

If system backups are available, the sample and tracing data can be restored by specifying a specified backup bucket (`HORTA_RESTORE_BUCKET`) and prefix (`HORTA_RESTORE_FOLDER`) which typically was created by the nightly backup job.
