---
title: "Tiled Imagery"
linkTitle: "Tiled Imagery"
weight: 4 
description: >
   Overview of how tiled imagery is loaded in Horta2D/Horta3D 
---

## Horta 2D
Horta's 2D and 3D viewer were originally 2 separate visualization packages, so the way that they load tile imagery is quite different.  Horta2D was called LVV (Large Volume Viewer) and exclusively loads many 2D slices from a TIFF Octree at a specific resolution to produce a mosaic that fills the window.  These 2D slices are retrieved from the jacs-sync REST API based off the current focus on the window.   

<div class="pb-3">
{{< imglink src="../LVV_Graphics.png" link="../LVV_Graphics.png" alt="Horta2D Class Diagram" width="700px" >}}
</div>

In the LVV Object Diagram above, the key classes to examine when dealing specifically with the UI are QuadViewUI, TraceMode, AnnotationPanel, SkeletonActorModel, and AnnotationManager.  QuadViewUI is the main Swing container for LVV, wrapping the 3D viewer and AnnotationPanel.  The AnnotationPanel is the Swing panel on the right of the layout that has all the menu options, neuron filter options and annotation lists for each neuron.  TraceMode is the main User EventManager, handling all key presses and mouse clicks.  The SkeletonActorModel is the Controller for all the Neuron tracing (anchors/vertexes and directed paths between them).

Horta2D has a different tile-loading mechanism, but shares all other data with Horta3D through TmModelManager, including the current focus location, selection, resolution, voxel-micrometer mapping, etc.  

## Horta 3D

Horta3D loads 3D tiles as 3D texture-mapped chunks of the type of imagery (KTX, TIFF, MJ2, Zarr) associated with the current workspace/sample.  These chunks all inherit from GL3Actor (TetVolumeActor, BrickActor), and are all loaded into a collection that is rendered by NeuronMPRenderer in its VolumeRenderPass.  The main JOGL OpenGL window is SceneWindow.

When Horta3D is first loaded, the focus location is initialized from the sample bounding box and center information that is calculated on scene/workspace loading.  The top component of Horta3D, NeuronTracerTopComponent, then delegates to the NeuronTileLoader to find the appropriate 3D chunks for that view.  The BrickSource objects are used to load 3D voxel data and convert it into a 3D object that can be rendered in the SceneWindow.  

In the case of KTX, the TetVolumeActor and KTXBlockLoadRunner work together to queue up a number of 3D chunks for multi-threaded loading based off the zoom resolution and current focus in the SceneWindow.  The intent is to load as many chunks are necessary to fill up the viewport.  

<div class="pb-3">
{{< imglink src="../horta3d_tile_loading.png" link="../horta3d_tile_loading.png" alt="Horta3D Tile Loading Diagram" width="700px" >}}
</div>


