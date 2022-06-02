---
title: "Concepts"
linkTitle: "Concepts"
weight: 10
description: >
    High-level description of the data, the software, and the jargon
---

## Data

Light microscopes of various kinds can now image an entire fly or mouse brain (eg, resonant scanning two photon with integrated vibrotome or Bessel light sheet). Horta is capable of displaying and annotating the resulting multi-terabyte datasets. The details of how the data should be prepared for use in Horta is covered in the [Horta Reference]({{< ref "/docs/user_manual/reference" >}}) section. In general, though, the data will have these properties:

- size: generally limited by disk space (local or cloud)
- channels: two channels are supported and tested; three channels are supported but not well tested; four or more channels are possible but would require more development
- location: the data will be on a storage system accessible to the image server 
    + (HortaCloud) AWS S3 bucket
    + (desktop) local or networked storage available to the workstation servers
    + the file path to the data should be known to the user if they will be creating samples manually 
- arrangement: the 2d and 3d images are arranged in multi-resolution octrees; details are covered in the reference section, but the user doesn't need to know them

## Software & tasks

Horta includes the Horta 2D viewer, which is the 2d, plane-by-plane, viewing and annotation tool, and the Horta 3D viewer, which supports viewing and annotating the data in three dimensions. The two viewers connect to common data editing and storage functions, many of which can be viewed in the Horta Control Center.

Horta is designed for a variety of tasks:

- viewing data: panning, zooming in and out, and scrolling through planes; brightness and contrast adjustment
- skeletal tracing of neurons: tracer places connected points on the signal
- text annotation: each point can have arbitrary text added to it
- import/export: skeletons can be imported or exported as SWC files; text notes are imported/exported as JSON (see reference section for details)

{{% alert title="Why the name 'Horta'?" color="primary" %}}
"Horta" is the name of a tunneling silicon based lifeform from the Star Trek original series episode 25 ["The Devil in the Dark"](http://en.wikipedia.org/wiki/The_Devil_in_the_Dark). Neuron traces in 3D resemble the tunnels such a creature might make in the ground. "Horta" can also be conveniently backronymed as "How Outstanding Researchers Trace Axons".
{{% /alert %}}

## Workflows

The basic tools can be used to implement a variety of workflows. For example, a user may trace a neuron entirely on their own. Or two users may trace the same neuron, export their results, and a later user may compare those results either by importing them both back in to the workstation or using other tools. If computational methods can trace neurons and export the results as SWC files, those results can be imported into the workstation for validation and/or correction and extension by later users. This flexibility supports neuron reconstruction at both small and large scales.

## Jargon

- basic objects in Horta: 
    + `sample`: the data; specifically, the object in the workstation that represents a particular image dataset; it's independent of user, and it's all you need if you are only viewing the data 
    + `workspace`: the object in the workstation that collects a set of annotations (traced neurons and text annotations); it is owned by a user or group, and it contains neurons 
    + `neuron`: the object that holds the traced neuron skeletons; a neuron usually contains one or more neurites 
    + `neurite`: a single skeleton or tree; a group of annotations with parent-child relationships, all tracing back to a single root without a parent 
    + `annotation`: a single point that may have zero or one parent and zero or more children; annotations are sometimes called anchors 
    + `tag`: a word or phrase attached to a neuron; it may be used in filtering the neuron list or in controlling neuron appearance 
    + `owner`: a username or group that owns the neuron; if a neuron is owned by a user, only they may change it; if a neuron is owned by a group, any member of the group may take sole ownership of the neuron (and then change it) 
- data and data formats: 
    + `tiles` or `octree`: the files holding the 2d data 
    + `ktx tiles`: the files holding the 3d data 
    + `SWC file`: a common multi-column text file format for storing neuron skeletons 
    + `JSON file`: a common structured text file format used to import/export text annotations
- tracing methods & workflows: 
    + `manual`: a tracer clicks along the signal in the data to create a neuron skeleton 
    + `semi-automated`: a computer program partially traces the signal in a dataset; the fragments are imported into the workstation; a tracer connects and/or extends fragments as needed to produce the skeleton
 