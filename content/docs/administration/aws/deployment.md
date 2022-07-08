---
title: "Deployment"
linkTitle: "Deployment"
weight: 10
description: >
  How to deploy HortaCloud to your own AWS account
---

The deployment uses AWS CDK to create AWS resources on your AWS account as shown in the diagram below. All services run in a secured Virtual Private Cloud (VPC).

{{< imglink src="../cloud_architecture.png" link="../cloud_architecture.png" alt="cloud architecture diagram" width="800px" >}}

## Install prerequisites

You should have **node v14** installed on your local machine. We recommend using [nvm](https://github.com/nvm-sh/nvm) to install and activate this version of node.

* Install AWS CLI
  * AWS CDK requires AWS CLI to be installed and configured on the computer from which one runs the deployment procedure. [Installation](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html) & [configuration](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) instructions can be found in the AWS documentation.

* Install AWS CDK by running

```bash
npm install
```

This command will install CDK in your development environment so you can access its help as below (notice the '--' separator between cdk and cdk options - this is specific to npm not to cdk so all CDK flags must be after the double hyphen separator):

```bash
npm run cdk -- --help
```

## Get the deployment scripts

Clone the [HortaCloud GitHib repository](https://github.com/JaneliaSciComp/hortacloud) containing the deployment scripts:

```bash
git clone https://github.com/JaneliaSciComp/hortacloud/
cd hortacloud
```

Install the dependencies:

```bash
npm run setup -- -i
```

## Configure environment

The following values must be set in the `.env` file:

```properties
AWS_REGION=<your aws region>
AWS_ACCOUNT=<your aws account>

HORTA_ORG=<app qualifier name>
ADMIN_USER_EMAIL=<admin email>

JACS_JWT_KEY=<a 32 byte jwt secret>
JACS_MONGO_KEY=<a 32 byte mongo secret>
JACS_APP_PASSWD=<app password>
RABBITMQ_PASSWD=<rabbitmq password>
JACS_API_KEY=<jacs api key>
JADE_API_KEY=<jade api key>
HORTA_DATA_BUCKETS=<s3 buckets that hold MouseLight data>
```

The api keys and secrets have been randomly generated during the setup step, but you can generate new ones with the following command:

```bash
openssl rand -hex 32
```

We prefer this procedure because these values will be handled during the installation using the `sed` command and it is preferable that they not contain any characters that require escaping in a sed command.

If you already have data on some S3 buckets you can add them to `HORTA_DATA_BUCKETS` as a comma separated list. For example, if you want to use Janelia's Open Data bucket but in addition you also have your data on a private bucket ('janelia-mouseligh-demo' in this example) you need to set `HORTA_DATA_BUCKETS="janelia-mouselight-imagery,janelia-mouselight-demo"`. Currently it is set to Janelia's open data MouseLight bucket only. Every bucket specified in the 'HORTA_DATA_BUCKETS' list will be available in the workstation as `/<s3BucketName>` directory.

If you want to change the setting for `HORTA_WS_INSTANCE_TYPE`, keep in mind that you may have to change `HORTA_WS_IMAGE_NAME`
For `HORTA_WS_INSTANCE_TYPE` set to any `stream.graphics.g4dn.*` instances:

* `stream.graphics.g4dn.xlarge`
* `stream.graphics.g4dn.2xlarge`
* `stream.graphics.g4dn.4xlarge`
* `stream.graphics.g4dn.8xlarge`
* `stream.graphics.g4dn.12xlarge`
* `stream.graphics.g4dn.16xlarge`

use: `HORTA_WS_IMAGE_NAME=AppStream-Graphics-G4dn-WinServer2019-07-19-2021` image.

For `HORTA_WS_INSTANCE_TYPE` set to any `stream.graphics-pro.*` instances:

* `stream.graphics-pro.4xlarge`
* `stream.graphics-pro.8xlarge`
* `stream.graphics-pro.16xlarge`

use `HORTA_WS_IMAGE_NAME=AppStream-Graphics-Pro-WinServer2019-10-08-2021` image

## Configure AWS account

### IAM Required Roles

In order to create an AppStream Image Builder, which is needed to create the Workstation Image, you need to have all [roles required by AppStream](https://docs.aws.amazon.com/appstream2/latest/developerguide/roles-required-for-appstream.html). Check that by simply connecting to the AWS console and check if the Roles are available in the IAM Service - select "Services" > "Security, Identity, Compliance" > "IAM" then verify that the required roles are present:

* AmazonAppStreamServiceAccess
* ApplicationAutoScalingForAmazonAppStreamAccess
* AWSServiceRoleForApplicationAutoScaling_AppStreamFleet

### AWS Limits

Most AWS services allow you to setup restrictions on the number of active instances. The default limits, especially for some AppStream resources, such as "Maximum ImageBuilders" for some graphics instances - "stream.graphics.g4dn.xlarge" may be really low (0 in some cases). Connect to AWS console "Service Quotas" service and increase the limit for in case you see a `limit was exceeded` error. Typically take a look at the limits setup for your account for EC2, VPC, AppStream, S3. Keep in mind that limits may be different from instance type to instance type for AppStream service, so you may have to adjust the limits based on the AppStream instance type selection.

## Deploy HortaCloud services to AWS

After the setup is complete, deploy the application by running:

```bash
npm run deploy
```

First time the application is deployed we also need to create user login pool and this must be explicitly specified using '-u' flag [See **Deploy the user login stack** section below]:

```bash
npm run deploy -- -u
```

There are a few steps during the deployment that require manual intervention. The deploy script will indicate when these steps should be taken with a ⚠️  warning message.

The full deployment of the application is done in 3, or 4 steps - if user login stack is deployed too, that run automatically one after the other,
with some manual intervention for **AppStream builder** step (third step outlined below):

1) **Deploy the user login stack** - this step is optional and practically is only needed first time the application is deployed. To create the user login stack you need to pass in '-u' flag to the deploy command (`npm run deploy -- -u`) which will automatically create a Cognito user pool and the 'admin' user and 'admins' group. You also have an option to import cognito users from a backup (`npm run deploy -- -u \
-r -b janelia-mouselight-demo -f hortacloud/backups/20220511030001/cognito`) but in this case you may need to skip the creation of the default admin user and group.

2) **Deploy the back-end stacks** - this includes the AppStream builder. At the back end deployment the installation process will also create the admin user configured in `ADMIN_USER_EMAIL`.

3) **Connect to AppStream builder and install the workstation application** - This is a semiautomated step that involves copying and running two PowerShell scripts onto the AppStream builder instance.

4) **Deploy the administration stack.**

### Workstation app installation

For client installation start and connect to the AppStream builder instance then copy the following scripts from this repo to the AppStream instance:

* [installcmd.ps1](https://github.com/JaneliaSciComp/hortacloud/blob/main/vpc_stack/src/asbuilder/installcmd.ps1) - installs JDK and the workstation
* [createappimage.ps1](https://github.com/JaneliaSciComp/hortacloud/blob/main/vpc_stack/src/asbuilder/createappimage.ps1) - creates the AppStream image

After you copied or created the scripts:

* Log in to the AWS console and go to <https://console.aws.amazon.com/appstream2>
* Find your new builder in the "Images > Image Builder" tab
* Click on the image name and open an "Administrator" window by clicking on the "Connect" button.
* Copy the installation scripts from your local machine to AppStream:
  * Click on the folder icon at the top left of the window
  * Select the `Temporary Files` folder
  * Use the `Upload Files` icon to find the files on your machine and upload them.
* Open the powershell by typing "`Power shell" in the search found at the bottom left of the window. This step used to require an "Administrator Power Shell" but now it needs only a regular user power shell and it may actually fail the install if you run it in an Administrator Power Shell.
* Change to the directory where you uploaded the installation scripts, eg:

```powershell
cd 'C:\Users\ImagebuilderAdmin\My Files\Temporary Files'
```

* Run the installcmd script to install the workstation. &lt;serverName&gt; is the name of the backend EC2 instance, typically it looks like `ip-<ip4 with dashes instead of dots>.ec2.internal`. Instructions for locating this are provided as output from the installer script.

```powershell
installcmd.ps1 <serverName>
```

  This will install the JDK and the workstation. The installer will run silently and it will install the workstation under the `C:\apps` folder. If it prompts you for the install directory, select `C:\apps` as the JaneliaWorkstation location.

* *Optional* - To start the workstation for testing, run:

```powershell
c:\apps\runJaneliaWorkstation.ps1
```

* when prompted, login as the admin user you set in ADMIN_USER_EMAIL (leave the password empty)
* Navigate through the menus to make sure the workstation is working. *Do not create any user accounts at this time as they will get created from the Admin web application.*
* When testing is finished, close down the workstation.

* Finalize the creation of the AppStream image, run:

```powershell
createappimage.ps1
```

  Keep in mind that once you start this step the builder instance begins the snap shotting process and it will not be usable until it completes. After this is completed the AppStream image should be available and the builder will be in a stop state. To use it again you need to start it and then you can connect.

* You can now safely close the AppStream session and return to the AppStream console. There you will see a new image in the image registry with a status of `Pending`.
* Once the image status has changed to a status of `Available` you can start the fleet by going to the `Fleets` page on the AppStream site.
  * Select your fleet from the list of fleets and then select 'Start' from the `Action` menu.
* At this point the installation script you started on your host machine, should continue to completion.

## Customizing the portal URL

By default the application will have a very long url that is not easy to remember, something like:
<http://janelia-hortacloudwebapp-janeliahortacloudwebadmi-yefcny29t8n6.s3-website-us-east-1.amazonaws.com/>. Follow these instructions to create a shorter domain for use with your installation.

* Register a domain with Route53 or your domain provider.
  * The Route53 page in the AWS console has a "Register domain" form.
  * Alternative providers can also be used, but it requires a little more work.
* Purchase an SSL certificate for your domain.
  * This can be done with [AWS Certificate Manager](https://aws.amazon.com/certificate-manager/)
  * or an external certificate provider, often it can be done with the same company that provided your domain registration. Use the "Import a certificate" button to register your certificate with AWS.
* Use the "Create distribution" button on the CloudFront console to attach your registered domain to the s3 bucket that hosts the admin portal.
  * the only things that need to be changed from the defaults are
    * "Origin domain" - this should be the domain that was originally generated for your admin portal.
    eg: *janelia-hortacloudwebapp-janeliahortacloudwebadmi-yefcny29t8n6.s3-website-us-east-1.amazonaws.com*
    * "Viewer protocol policy" - Change this to "Redirect HTTP to HTTPS"
    * "Custom SSL certificate" - Select the certificate that you registered with AWS Certificate Manager
  * Finally, click the "Create distribution" button.

## Upgrading HortaCloud services to AWS

### Brute Force Approach

This method requires removing (uninstalling) the existing HortaCloud stack and then reinstalling it. Because of the requirement to remove the existing installation the approach there is a relatively high risk of loosing the data, so before starting anything it is best to check that the latest backup or some backup is available. Upgrading the application typically only requires a backend and an application frontend upgrade - without any need to migrate the users so you only need to check if a backup for the JACS Mongo database exists typically at `s3://<HORTA_BACKUP_BUCKET value>/<HORTA_BACKUP_FOLDER value>/<timestamp>/jacs`

To uninstall current HortaCloud instance run:
```bash
npm run destroy
```

This command will uninstall the frontend and the backend AWS Cloudformation stacks, i.e., Admin, Appstream and JACS stacks, but it will not uninstall Cognito stack, so no user account will be removed.

The next step is to upgrade the local git repo using
```
git pull
```
followed by redeploying the application.

In order to restore the database from an existing backup make sure the following properties are set:
```
HORTA_RESTORE_BUCKET=<backup bucket name>
HORTA_RESTORE_FOLDER="/hortacloud/backups/latest"
```
HORTA_RESTORE_BUCKET is the name of the backup bucket and HORTA_RESTORE_FOLDER must reference the parent prefix containing the 'jacs' folder - the location of the actual mongo backup. Typically the backup job creates a "softlink"  - "/hortacloud/backups/latest" so if you simply set the restore folder to that it should pick up the latest backup. If the backup was a manual backup or you need to restore to a previous date set the restore folder to that folder. For example setting `HORTA_RESTORE_FOLDER=/hortacloud/backups/20220609030001` will restore the database to the content saved on "Jun 9, 2022".

After setting these properties you can proceed with the actual deploy procedure which will only install the backend and the frontend stack (skipping any Cognito installation):
```bash
npm run deploy
```

If somehow you need to recreate the user login accounts because you inadvertently removed the Cognito stack as well (using '-u' flag) you can restore all the accounts from a previous backup using the following command:
```
npm run deploy -- -u -r -b <backup bucket> -f hortacloud/backups/manual-backup/cognito
```

The folder parameter must point to the actual cognito prefix, where 'users.json' and 'groups.json' are located

### Incremental approach

Incremental approach is more manual but it does not require any data restore. It basically removes only the frontend stacks, i.e. Appstream and admin app and it requires a manual update of the backend stack and of the workstation. The steps for the incremental approach are the following:
* Remove only the frontend stacks:
    ```npm run destroy -- -b```
* From the AWS console connect to the EC2 instance (`<ORG>-hc-jacs-node-<STAGE>`) running the JACS stack.
* Once connected run the following commands
    ```
    cd /opt/jacs/deploy
    ./manage.sh compose down
    sudo git pull origin stable
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
    npm run deploy
    ```
  If no changes were made to the code AWS CDK will only update the missing stacks, leaving JACS stack as it was since it practically has not been changed from AWS' perspective

## Uninstalling HortaCloud services to AWS

To completely uninstall the application run:
```bash
npm run destroy -- -u
```

The command will uninstall all stacks including the user logins (Cognito) stack.

Note in the previous [system upgrade section](#Upgrading_HortaCloud_services_to_AWS) that an upgrade typically does not require removing and recreating the user pool stack.

## Troubleshooting

### Troubleshooting client app installation

If the client app installation fails for any reason, before you attempt the install again you must remove everything that was installed by the install script. Uninstall all applications installed with scoop and remove the 'C:\apps' folder. To do that run:

```powershell
scoop uninstall scoop
del c:\apps
```

When prompted whether you really want to uninstall everything, select "yes" or "all".
