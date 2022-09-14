---
title: "Upgrading"
linkTitle: "Upgrading"
weight: 40
description: >
  Upgrading to a new version of HortaCloud
---

There are two ways to upgrade to a new version of the code: [Full Upgrade](#full-upgrade) and [Incremental Upgrade](#incremental-upgrade).

## Full Upgrade

This method backs up all of the data, removes the existing HortaCloud stack, installs a new version from scratch, and restores the data. This is currently the most automated way to upgrade, and requires the least amount of effort.

### Backup your data

First, ensure that you have a recent data backup. If you have not [configured backups](../backups), do that first. For most upgrades, you only need to check if a backup for the JACS Mongo database exists. Typically this is located at `s3://<HORTA_BACKUP_BUCKET value>/<HORTA_BACKUP_FOLDER value>/<timestamp>/jacs`

### Remove HortaCloud environment

To remove the current HortaCloud environment run this command:

```bash
npm run destroy
```

This command will uninstall the frontend and the backend AWS Cloudformation stacks, i.e., Admin, Appstream and JACS stacks, but it will not uninstall Cognito stack, so no user accounts will be removed.

### Deploy new version of HortaCloud

The next step is to upgrade the local git repo:

```bash
git pull
```

You should also check out a specific version tag that you want to deploy:

```bash
git checkout <version>
```

In order to restore the database from an existing backup make sure the following properties are set:

```
HORTA_RESTORE_BUCKET=<backup bucket name>
HORTA_RESTORE_FOLDER="/hortacloud/backups/latest"
```

HORTA_RESTORE_BUCKET is the name of the backup bucket and HORTA_RESTORE_FOLDER must reference the parent prefix containing the 'jacs' folder - the location of the actual mongo backup. Typically the backup job creates a "softlink"  - "/hortacloud/backups/latest" so if you simply set the restore folder to that it should pick up the latest backup. If the backup was a manual backup or you need to restore to a previous date set the restore folder to that folder. For example setting `HORTA_RESTORE_FOLDER=/hortacloud/backups/20220609030001` will restore the database to the content saved on Jun 9, 2022.

After setting these properties you can proceed with the actual deploy procedure which will only install the backend and the frontend stack (skipping any Cognito installation):

```bash
npm run deploy
```

From here on, this approach is identical with the initial deployment, with the only exception that data and users should already exist once the backend stack is fully deployed.

After you start the deploy command, follow the instructions you see on the screen which will prompt you when you need to setup the AppStream Builder exactly as it is described in the [Workstation app-installation section](#workstation-app-installation)

If somehow you need to recreate the user login accounts because you inadvertently removed the Cognito stack as well (e.g. using `-u` flag) you can restore all the accounts from a previous backup using the following command:

```bash
npm run deploy -- -u -r -b <backup bucket> -f hortacloud/backups/manual-backup/cognito
```

The folder parameter must point to the actual cognito prefix, where 'users.json' and 'groups.json' are located

## Incremental Upgrade

Incremental approach requires more manual steps, but it does not require any data restore. It basically removes only the frontend stacks, i.e. Appstream and Admin App, and it requires a manual update of the backend stack and the workstation.

The steps for the incremental approach are the following:

* Remove only the frontend stacks:
    ```npm run destroy -- -b```
* From the AWS console connect to the EC2 instance (`<ORG>-hc-jacs-node-<STAGE>`) running the JACS stack using the "Session Manager". To be more specific, from the EC2 instances page, select the instance `<ORG>-hc-jacs-node-<STAGE>` and click on the "Connect" button. This will take you to the instance page, and then from there select the "Session Manager' tab and click the "Connect" button again. The "Connect" button should be enabled - if it's not there either was a problem with the deployment or there might be a problem with the instance itself.
* Once connected run the following commands

    ```
    cd /opt/jacs/deploy
    ./manage.sh compose down
    sudo git pull origin stable
    sudo git checkout <version>
    ./manage.sh compose up -d
    ```

* Start AppStream builder (`<ORG>-hc-image-builder-<STAGE>`)
* Connect as Administrator
* Check that 'reinstallwsonly.ps1' script is available, in the Admin's home directory. If not copy it from the application repo or just create it like the other install scripts, ('installcmd.ps1' and 'createappimage.ps1') were created on the initial deployment.
* Run the reinstallwssonly.ps1 script:

    ```
    .\reinstallwssonly.ps1 <IP of host running JACS stack>
    ```

    The IP of the host running JACS is the same used for initial run of 'installcmd.ps1' and it can be found from the AWS console.
* Try out the workstation application to make sure it works

    ```
    C:\apps\runJaneliaWorkstation.ps1
    ```

* Make sure you have the latest 'createappimage.ps1' script from the application git repository. If you don't copy the latest script that supports '--skip-registration' flag.
* Start the script for creating the AppStream image but skip the application registration: `.\createappimage.ps1 --skip-registration`
* Reinstall the frontend stacks

    ```
    npm run deploy -- --skip-vpc
    ```

  If no changes were made to the code AWS CDK will only update the missing stacks, leaving JACS stack as it was since it practically has not been changed from AWS' perspective
