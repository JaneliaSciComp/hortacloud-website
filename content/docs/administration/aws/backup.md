---
title: "Backup"
linkTitle: "Backup"
weight: 10
description: >
  How to backup your HortaCloud data
---

The data that is currently backed up is the Mongo database and the Cognito users. The backup is enabled automatically, if and only if at the deployment time the `HORTA_BACKUP_BUCKET` environment variable is set. If no such environment variable is specified no data will be backed up. In addition to the backup bucket you can also specify the base prefix for the backups using `HORTA_BACKUP_FOLDER`, but if this is not specified the prefix will default to "/hortacloud/backups". The location of the backups will be `s3://<backup_bucket>/<backup_folder_prefix>/<timestamp>/jacs` for the database and `s3://<backup_bucket>/<backup_folder_prefix>/<timestamp>/cognito` for cognito users.

The backup is configured as a cron job that runs daily at 3 AM local time on the EC2 host on which the backend JACS stack runs. At this time the job scheduling is not configurable, but nevertheless it is possible to change it by connecting to the EC2 instance and changing the corresponding cront entry.
