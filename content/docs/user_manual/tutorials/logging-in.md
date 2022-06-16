---
title: "Logging in"
linkTitle: "Logging in"
weight: 50
description: >
    How to log in to HortCloud and start the Horta application
---

In this tutorial, we'll go over the steps needed to log into HortaCloud and start the Horta application.

## Prerequisites

- You should know the URL for the HortaCloud instance that you will be using.
- The administrator of the instance should have created an account for you.
- You should know the username and password for that account.

The screenshots below were taken from the Janelia instance in June 2022.

## Steps

The steps to log in to HortaCloud and starting the Horta application are similar to the steps for many web applications. The procedure is straightforward, but we've provided screenshots of every step to help troubleshooting when things go wrong.


### Navigate to your HortaCloud website's URL in your browser.

You are probably already on the site, if you're reading this documentation! Click the button on the right labeled "Login to HortaCloud".

All major browsers are supported: Safari, Google Chrome, Mozilla Firefox, and Microsoft Edge. 

{{< imglink src="../images-logging-in/1-front-page.png" link="../images-logging-in/1-front-page.png" alt="HortaCloud home screen" >}}


### Enter your username and password given to you by your local administrator in the entry fields, then click the "Login" button.

On this page, there are also links for changing your password and for requesting a password reset if you have forgotten your password.

{{< imglink src="../images-logging-in/2-login-blank.png" link="../images-logging-in/2-login-blank.png" alt="HortaCloud portal username and password screen" >}}


### Click "AppStream Login" to continue.

This screen also contains a change password link, and if you are a Horta application administrator, you will also see tools for managing users and groups in Horta.


{{< imglink src="../images-logging-in/2-login-portal.png" link="../images-logging-in/2-login-portal.png" alt="HortaCloud portal when logged in" >}}


### Click "Horta Workstation" to start the application.

This is currently the only application in HortaCloud.

{{< imglink src="../images-logging-in/3-app-list.png" link="../images-logging-in/3-app-list.png" alt="AppStream application list" >}}


### Wait for application startup

As the application starts, which can take a couple minutes, you will see a wait screen (first image below). The percentage should steadily increase. After that, you will see a brief console screen (second image below), then the Horta application itself (third image below).

The list of folders you see in the "Data Explorer" in Horta will be different and will depend on what data is available in your instance and which data you personally have permissions to see.

{{< imglink src="../images-logging-in/4-wait-screen.png" link="../images-logging-in/4-wait-screen.png" alt="AppStream wait screen" >}}

{{< imglink src="../images-logging-in/5-console.png" link="../images-logging-in/5-console.png" alt="console" >}}

{{< imglink src="../images-logging-in/6-Horta.png" link="../images-logging-in/6-Horta.png" alt="Horta" >}}



## Troubleshooting

In most cases, if you have trouble launching the Horta application, you should contact your local HortaCloud administrator.  If the application launches but you do not see the expected data, you should contact the local Horta application administrator.  Of course, these may be the same person.


## Ending your session

You can end your session by clicking on the rightmost icon on the AppStream toolbar and choosing "End Session".

{{< imglink src="../../AppStream-toolbar.png" link="../../AppStream-toolbar.png" alt="AppStream toolbar" width="761px" >}}