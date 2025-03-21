---
title: "Client properties"
linkTitle: "Client properties"
weight: 100
description: >
  Configuring the Janelia Workstation client
---

## Properties

The Janelia Workstation is configured by a set of properties which define the behavior of the client.

* `client.Title` - Application title (shown in the title bar)
* `client.versionNumber` - Application version (shown in the title bar)
* `api.gateway` - URL of the default backend server to connect with
* `domain.msgserver.url` - URL of the RabbitMQ server
* `domain.msgserver.useraccount` - Username for the RabbitMQ server
* `domain.msgserver.password` - Password for the RabbitMQ server
* `console.startPage.showOnStartup` - Show the start page after the client starts? (`true` or `false`)
* `console.LVVHorta.tracersgroup` - Fully-qualified (starting with `group:`) subject name of the tracers group
* `console.LVVHorta.activetracersgroup` - Fully-qualified (starting with `group:`) subject name of the active tracers group
* `console.localCache.rootDirectory` - Path to local disk cache
* `console.AutoSendExceptions` - Send uncaught exceptions automatically? (`true` or `false`)
* `console.ErrorReportingMethod` - Method to use for error reporting (`email` or `github`)
* Email-based error reporting
    * `console.MailServer` - Hostname and port for the mail server
    * `console.MailUser` - Username for the mail server (may be empty for anonymous)
    * `console.MailPassword` - Password for the mail server (may be empty for anonymous)
    * `console.MailSSLProtocol` - Whitespace separated list of SSL protocols to allow (passed directly to JavaMail's `mail.smtp.ssl.protocols` property.)
    * `console.FromEmail` - Sender email on error reports
    * `console.HelpEmail` - Destination email for error reports
* GitHub-based error reporting
    * `console.GitHubErrorProjectURL` - HTTPS URL for the project receiving the reported issues
    * `console.GitHubErrorProjectAccessToken` - GitHub [personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) (string beginning with `github_pat_`)

Additional properties are described in the files linked below.


## Specifying Properties

At runtime, the client loads properties files in the following order, each subsequent load overridding previously loaded values:
* [console.properties](https://github.com/JaneliaSciComp/workstation/blob/4e7b5ac7eba0034a97d15e08d8e74619785c4a46/modules/Core/src/main/resources/console.properties#L84) - defined in the codebase
* [my.properties](https://github.com/JaneliaSciComp/jacs-cm/blob/master/containers/workstation-site-horta/my.properties) - defined during container build
* [client.properties](https://github.com/JaneliaSciComp/jacs-cm/blob/master/containers/jacs-init/filesystem/api-gateway/client/client.properties) - defined on the server side

Certain special properties (listed below) may be specified on the command-line, using Java System Properties as [shown here](https://github.com/JaneliaSciComp/hortacloud/blob/9.22.1/vpc_stack/src/asbuilder/installcmd.ps1#L141-L153):
* `api.gateway`
* `user.home`
* `console.serverLogin` 
* `console.rememberPassword`
* `SessionMgr.ShowReleaseNotes`
* `Login.Disabled`

## Property Values

Properties are specified with a property name, equals, and the property value. Previously defined properties may be referenced with brackets:
```
api.gateway=https://hostname
discovery.rest.url={api.gateway}/SCSW/ServiceDiscovery/v1/
```

## Configuration 

### Bare metal

The `init-filesystems` command deploys a template `client.properties` file to `${REDUNDANT_STORAGE}/config/api-gateway/content/client.properties`. You can edit this file to set the properties you want. 


### AWS

The default `client.properties` file for HortaCloud exposes property values as environment variables configured in the HortaCloud `.env.config` file. See the [client.properties](https://github.com/JaneliaSciComp/jacs-cm/blob/master/containers/jacs-init/filesystem/api-gateway/client/client.properties) file for the mapping of environment variables to properties. 

This subset of variables can be configured by `.env.config` file via restarting the Docker Compose or Docker Swarm stack.
