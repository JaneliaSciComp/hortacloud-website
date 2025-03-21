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
* `console.startPage.showOnStartup` - Show the start page after the client starts? ("true" or "false")
* `console.LVVHorta.tracersgroup` - fully-qualified (starting with "group:") subject name of the tracers group
* `console.LVVHorta.activetracersgroup` - fully-qualified (starting with "group:") subject name of the active tracers group
* `console.localCache.rootDirectory` - path to local disk cache

Additional properties are described on the [Error Reporting](../errorreporting/) page, and in the files linked below.


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



