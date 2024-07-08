---
title: "User manual"
linkTitle: "User manual"
weight: 5
description: >
    How to use the Horta application
---

The sections below describe Horta's basic and advanced features. Generally the sections listed below should be read in the order listed.

### Note on Horta versions
The Horta application exists in two versions that come from a common code base. The _cloud_ version (HortaCloud) that is available here is a reduced version of the _desktop_ version ([Janelia Workstation](https://github.com/JaneliaSciComp/workstation)). In fact, we'll often use the word "workstation" to refer to the Horta application in either form. 

When there are differences between the two versions, they will be noted with "(HortaCloud)" and "(desktop)". If the differences are more substantial, they will be presented like this:

{{% alert title="HortaCloud only" color="primary" %}}
In HortaCloud, the following control behaves differently from the desktop version.
{{% /alert %}}

{{% alert title="Desktop only" color="primary" %}}
The following features is only available in the desktop version of the Janelia Workstation.
{{% /alert %}}

The major user-visible differences between the two versions are:
- HortaCloud does not have access to the local file system or clipboard and requires additional steps to import or export data via those routes (see AppStream Basics section for details)
- HortaCloud datasets typically only include low-resolution 2D data, so some 2D tools are not as useful or may not function in the absence of high-resolution data (eg, automatic path tracing)
- the desktop Janelia Workstation contains tools for viewing, annotating, and managing other unrelated Janelia datasets


