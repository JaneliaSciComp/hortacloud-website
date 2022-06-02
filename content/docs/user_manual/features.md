---
title: "Features"
linkTitle: "Features"
weight: 40
description: >
    Horta features
---

This section discusses Horta features. It contains both details of basic operations that are covering in the [previous section]({{< ref "/docs/user_manual/basic_operations" >}}) as well as descriptions of features that haven't yet been discussed.


## Global

### Preferences/settings

There are a number of customization options for Horta.

{{% alert title="HortaCloud" color="primary" %}}
In HortaCloud, choose Tools > Options.
{{% /alert %}}

{{% alert title="Desktop" color="primary" %}}
If you're running Windows, choose Tools > Options if you're running Windows. On a Mac, the settings are called "Preferences" and are in the traditional place under the application menu at top right.
{{% /alert %}}

Here are some settings of interest:

- `Core > Application`: 
    + Max Memory (desktop): This should be set to a high number, preferably 40G or more. 
    + Max Memory (HortaCloud): This should be left blank; the system will set the memory correctly.
- `Horta > Application`:
    + "Use http for file access": Must be left checked.
    + "Load last opened sample...": This option is not currently working.
    + "Verify neurons on load" is never needed under normal circumstances. It can be enabled to check for some internal inconsistancies in neurons stored in the database.
    + "Use anchors-in-viewport" should be left enabled. This is a graphics optimization. 
    + See below for "Click mode for adding annotations"
    + "Z-slice thickness": The 2D view shown in Horta 2D actually shows annotations from multiple planes. This setting determines the depth of this effect. If you increase the number, annotations become visible on a large number of surrounding planes. Note however that there are some subtle behaviors in how the data and annotations are rendered, and changing this value dramatically away from its default value may not have the effect you want.
- `Horta > Tile Loading`: 
  + In general, you can leave "Concurrent tile loads" and "Number of tiles to cache" to their defaults. Larger numbers _may_ improve tile loading in Horta 3D. 
  + See below for "Click mode for adding annotations"
- `Keymap`: 
  + This section shows all of the user-changeable keyboard shortcuts for the Janelia workstation, including Horta. The shortcuts are listed by section. 
  + New shortcuts will take effect after the application is restarted.
  + Note that some keyboard shortcuts in Horta cannot be changed from this dialog. Unfortunately, there is no central list of what they are. 


#### Click mode for adding annotations

By default, to add annotations in Horta 2D and 3D, you select a parent annotation, then you shift-left-click on the desired location. For some people, holding down the shift key while making repeated annotations may cause stress on their hands, fingers, or wrists when done over a long period of time. For that reason, an option is provided to change that gesture to a simple left-click to add annotation (with no shift key). Note that this is set independently for Horta 2D and Horta 3D. The two settings are in different subpanels for historical reasons.

Note that if the "left-click" option is selected, some other behaviors in the two viewers will change, specifically those involving left-clicks. Most notably, in Horta 2D, idle clicks when (for example) closing right-click menus will register as an annotation.

Generally speaking, using the default group names:

- all users should be a member of `workstation_users`
- all users who want to interact with Horta samples or annotations should be a member of `mouselight`
- all regular Horta tracers should _also_ be a member of `mouselight_tracers`; this group is used to populate some dialog boxes regarding neuron ownership, and project leadership, collaborators, and other non-tracers need not be in this group (it'll clutter the menus)

### User and group administration

If you are an administrator, you can Windows > Core > Administration tool to manage users and groups. Note that these are users and groups within the Horta or Janelia Workstation applications. For HortaCloud, there is a second layer of users and administrator for AppStream that are managed separately.

An administrator within the Horta or Janelia Workstation system has a lot of power.

- see all data and assign permissions for all data
- set privileges for all users and groups
- use Tools > Admin > Run as... to connect as any user (usually used for debugging purposes)

You should limit the number of people with admin privileges.


## Horta Control Center features

### Notes

Notes are text annotations that are attached to point annotations. They can be added, edited, or deleted from a dialog box triggered by a right-click on the annotation and choosing "Add, edit, delete note". The contents can be arbitrary. 

However, the note dialog does provide several pre-defined notes that can be used to support a simple tracing workflow. In addition, some of those pre-defined notes have specific effects on other operations and on the annotation filters.

| predefined note | intended use | behavior |
| --- | --- | --- |
| traced end | to be added when the signal at that point has been traced as far as it can; it's a "done" marker |<ul><li>must be placed on an endpoint</li><li>if another point is placed as a child, it's automatically removed</li><li>does not appear in the "ends" filter</li></ul> |
| branch | to be added when the tracer finds a point where signal branches but doesn't want to follow the branch at that time; it's a "come back to this" marker | <ul><li>cannot be placed on an existing branch</li> <li>if another point is placed to make it an actual branch, it's automatically removed</li> <li>does not appear in the "branches" filter</li></ul> |
| interesting | indicates a point of interest | no special behavior |
| review | indicates a point that needs review by a second set of eyes, supervisor, or expert | no special behavior |
| problem end | indicates an endpoint that may or may not be able to be traced further by the current user; it's a "done (but problematic)" marker | <ul><li>must be placed on an endpoint</li> <li>if another point is placed as a child, it's automatically removed</li> <li>does not appear in the "ends" filter</li></ul> |
| (arbitrary text) | the user may place arbitrary text at any point | no special behavior |

All together, the notes and filters together were designed to support the following workflow:
    - start tracing at a soma or a segment of interest
    - add point along the segment
    - at a branching point in the signal, add the "branch" note to the annotation to mark it as a point to be revisited; there is then no need to place a single point on the second branch to find it later
    - when a branch ends in a synapse or becomes too faint to trace, place a "traced end" or "problem end" note, indicating that the tracing is complete for this branch
    - if those notes are added consistently, then the "branches" and "ends" filters will identify those locations that need further work
    - when those two filters have no annotations in them, the neuron is done

### Tags

Tags are user-defined, usually short, labels that are applied to neurons.

- To add or remove tags from a single neuron, right-click on the neuron in either the Horta 2D view or the neuron list and choose "Edit neuron tags". 
    +  The left "Applied" column contains tags that have already been applied to the neuron. Click on a tag in this column to removed it. 
    +  The right "Available" column contains existing tags that can be applied to the neuron by clicking on them. 
    +  To define a new tag, type the tag in the box below the "Available" column and click "New tag". It will be created and applied immediately. 
    +  The list of "available" tags is the list of tags that exist on any neuron, plus two special tags. 
    +  The "auto" tag may be used freely. It is suggested to use this tag to mark neurons that were created by some automated process, to distinguish them from human-created neurons. When automatically created neurons are numerous, this can be used to filter them out (See "Spatial filter"). 
    +  The "hidden" tag is used internally and shouldn't be used. 
-  To add or remove a tag from multiple neurons, choose "Edit tags" from the gear menu in the neuron list. The tag will be added or removed from all the neurons in the filtered list, if applicable. 
-  Tags can be used to filter the neuron list by either including or excluding neurons with a single tag.

Tags can be used to control a few useful toggle behaviors. This feature is called "Neuron group properties".

- First, add a tag to one or more neurons of interest using one of the above methods. 
- Right-click on a neuron and choose "Edit neuron group properties". 
- The dialog box shows each user-applied tag as a "Neuron group" and indicates how many neurons are in the group. 
- Click on the cell in the third column of a row corresponding to a tag to assign a hotkey for the toggle. 
- Click on the fourth column to assign a toggleable property. 
- Click save. Now when keyboard focus is in Horta 2D, the hot key will toggle the property: 
    + Radius: the radii of the neuron(s) in Horta 3D will be toggled between their set size (either default or user-set) and a very thin radius 
    + Visibility: the hotkey will hide or show the neurons(s) 
    + Background: when this is toggled on, the neuron is placed in a "background" state that can be seen but not interacted with (ie, you cannot move, select, or add points to a background neuron) 
    + Crosscheck: this is the same as "background" and "radius" together; it's designed to have a neuron visible but unobtrusive and unchangeable 
- Note that this feature has been buggy in the past; sometimes you need to save repeatedly before the setting holds.

### Shared workspaces & the Neuron Broker: updates

When multiple users open and work in the same workspace, the Neuron Broker on the server acts as the intermediary for all changes to the database.

- It ensures that changes occur one at a time, in order. 
- It prevents users from changing neurons they do not own. 
- It broadcasts the results of changes to all users, so users in the same workspace can see changes made by other users.

There are two elements of the UI that relate to the operation of the Neuron Broker.

- First, if "Shared Updates" is unchecked in the UI (in the right side panel of the Horta 2D view), then changes in the workspace will not be displayed until the "Refresh" button is clicked (same panel). This can be useful in the rare occasions that so many updates are arriving that it affects local performance. Normally, this should be left on. 
- Second, if the name of a neuron in the neuron list becomes italicized, that is an indication that the data in the workstation has become unsynchronized with the data in the database for that neuron. If you hover the mouse over the neuron name, it will tell you how many changes are not synced. Generally, if this occurs and does not clear after a minute or two, you should reopen the workspace. The unsynced changes will be lost, but the workspace will then be fully in sync with the database.

See also "Changing neuron ownership" in the Horta Features section.


### Spatial filtering

When a workspace has a large number of neuron fragments, such as those created by an automated segmentation process, two negative consequences may occur. First, the display becomes visually cluttered, and second and more important, performance of all kinds can degrade. These problems can be alleviated by only loading and displaying a small subset of all fragments in the region where the tracer is working. To enable spatial filtering:

- Click the gear menu under the neuron list and choose "Set Neuron Filter Strategy". 
- The first check box enables or disables the filter. 
    + In all cases, only fragments owned by the tracing group are affected. Neurons owned by tracers (current tracer or other tracers) will always be loaded. 
- There are currently two options for the filter: 
    + Proximity: fragments will be loaded if they are close to any non-fragment neuron. 
    + Selection: fragments will be loaded if they are close to the last selected annotation. 
- Distance: in either strategy, this controls how close fragments need to be to the target neuron(s) or point in order to be loaded.

### Task workflow & Neuron Cam

This feature is still being developed and will be documented later.

### Movie Maker and Scene Editor

The Horta Movie Maker is designed to created fly-through movies, typically for presentations. It is not supported and not documented at this time.


## More Horta 2D features

### Right-click operations

Horta 2D has a number of operations on its right-click menu. Many of them are described elsewhere, especially in the [Horta Basic Operations]({{< ref "/docs/user_manual/basic_operations" >}}) section. We will quickly summarize most of the operations here. Operations that are not documented below fall into two categories. First, some of them are redundant with other parts of the UI and are likely to be removed from the menu. For example, "Begin new neurite here" is unnecessary on the right-click menu. That operation is also documented in the Horta Basic operation section. Second, if an operation is not documented anywhere, it is deprecated and likely to be removed entirely, not just from this menu. Some of these will be noted explicitly.

#### Right-click in empty space

Some options appear when you click in empty space in the 2D view, with no annotation under the mouse pointer.

- Scroll through Sample (Z): The camera will move to the lowest plane (smallest z) at the mouse pointer location and smoothly move through the planes until it reaches the highest plane. The zoom level is not changed. 
- File: All the operations on this menu are deprecated and should not be used. 
- Copy (various): When any of these options are selected, information about the location of the mouse pointer is copied to the clipboard. Note that for HortaCloud, the data is copied to the AppStream clipboard, and you must click the icon in the toolbar to copy it to the clipboard of your local computer
    + Micron coords: the x, y, z location in µm in this form: `[75556.8, 43819.6, 23460.8]` 
    + Octree location: the location in the file hierarchy of the 2D tile under the mouse pointer. See the Horta Reference section for more details on the file layout. The format is this: `/5` 
    + Raw file tile location: the file path to the raw microscope tile that produced the rendered data under the mouse pointer, if available. 
    + Octree filepath: the file path to the rendered tile under the mouse pointer. 
- Task Workflow: see below 
- View: The view submenu replicates a number of buttons on the Horta 2D toolbar and side navigation panel. It is redundant and likely to be removed at some point in the future. 

#### Right-click on an annotation

If you right-click on an annotation in Horta 2D, a longer set of operations appears in addition to those described above. Note that an annotation will become visibly slightly larger when the mouse pointer is over it, indicating that the annotation will be the target of the click. Again, some of the operations are either deprecated or may be removed from the menu.

- Trace path to parent: generate an automatically traced path from the selected annotation to its parent (see "Automatic point refinement and tracing", below) 
- Delete subtree: deletes the annotation under the mouse pointer and all annotations in the tree below it 
- Delete link: deletes the annotation under the mouse pointer if it is an endpoint, an isolated single point, or a point with a single parent and child (in which case the parent and child are connected). You cannot delete a branch point or a root point with children; use "delete subtree" instead. 
- Merge chosen neurite...: see "Smart merge" below 
- Transfer neurite: As described in Horta Concepts, a "neurite" is a structure rooted at a single root annotation, while a "neuron" is a container that may hold several neurites. This operation moves the selected neurite to another existing neuron (chosen from a dialog), or to a newly created neuron, which you are prompted to name. 
- Split anchor: This operation creates a new annotation between the selected annotation and its parent, placed close to the selected annotation. It's used to increase the density of points in the neuron. 
- Split neurite: This operation splits the current neurite into two, with the selected annotation cut from its parent into a new root. 
- Add, edit, delete note: See "Notes" section below. 
- Edit neuron tags/group properties: See "Tags" section below. 
- Generate neuron review tree/Select point in Task View: See "Task workflow" section below. 
- Set neuron radius: This operation allows the user to set the radius associated with the annotation. By default it is set to 1.0 µm.

### Automatic point refinement and path tracing

{{% alert title="Desktop only" color="primary" %}}
These features only work in the desktop version of Horta. HortaCloud data does not contain the high-resolution 2D data needed to make them work.

If you are working on a rare dataset that _does_ have the full 2D octree in HortaCloud, it should work.
{{% /alert %}}

- Automatic point refinement: This option can be toggled on and off from the gear menu in the workspace information area. It only applies to Horta 2D.
    + When this feature is toggled on, annotations will be placed at the x, y location of the click as usual. However, the annotation will be placed on the z-plane which has the brightest signal in the first channel within five planes of the click. 
- Automatic path tracing: This option can be toggled on and off from the gear menu in the workspace information area. 
    + When this feature is toggled on, when the user adds a new annotation, Horta 2D places an additional set of annotations between the new annotation and its parent. 
    + The "automatic path" is calculated automatically to follow the signal in three dimensions in the first channel using an A-* (A-star) algorithm. The details are contained in the reference section. The points are placed densely, approximately one pixel apart. 
    + These paths are also drawn in a different style than the links between manually-placed points. 
    + While the toggle is active, the paths are automatically recalculated whenever any manually-placed annotation changes. So if an annotation is moved by hand, all of the links to its parent and children will be updated with new automatic paths. 
    + If you are using this feature, you should place annotations not too far apart. The algorithm is limited so it will not run more than a handful of seconds, and if points are too far apart, the algorithm may not find a path before it runs out of time. 
    + These points may be exported as other points are. The "Export SWC" dialog contains one option to let you control how many points are exported. The "Point density" value should be set to 0 if you don't want to export any automatically placed points, set to 1 if you want to export all of them, or to a positive integer n if you want to export every nth point.

Note that you may request an automatic path tracing from an annotation to its parent explicitly from the right-click annotation menu.

### Smart merging

**Note**: this feature is experimental!

When presented with a large number of neuron fragments that must be assembled into reconstructed neurons, such as those provided by automated image analysis, the amount of time merging each neuron pair should be minimized. The "smart merge" feature tries to make it easier for the user to do a large number of repeated merges. 

- To use this feature, select any point on the first neuron fragment. Then right-click on any point in the second neuron fragment and choose "Merge chosen neurite to selected neurite". 
- Horta 2D will attempt to find the two endpoints in the two fragments that are closest. 
- Horta 2D will then move the point selection (the next parent selection) to the farthest endpoint in the merged neuron.

### Arrow key navigation

You may navigate along the neuron backbone in Horta 2D using the arrow keys. With a point in a neuron selected (set as next parent), pressing an arrow key moves the selection to another point and recenters the screen on that point.

- Right arrow: moves to the next branch point or endpoint farther away from the neuron root; if there are multiple possible branches, the first is followed (see below) 
- Left arrow: moves to the previous branch point or root toward the root 
- Alt + left/right arrow: as before, but move by only one point instead of until next branch/root/end 
- Up/down arrows: 
    + In general, the up and down arrows move you among parallel branches 
    + When on a neuron segment that has another segment in parallel (another segment with the same branch point parent), move to the next such branch in sequence, positioned at the first point beyond the branch point. 
    + When on a branch point or root, or segment without parallel branches, does nothing. 
- The order of branches in all cases ("first" for right arrow, or parallel branches for up/down arrow) is determined by the order the children of the branch appear in internal data structures 


### Other
The "Volume cache" checkbox and "Clear cache" button to the right of the 2D view area should generally be ignored.

{{% alert title="Desktop" color="primary" %}}
Since the desktop version of Horta uses the full 2D octree, prolonged browsing in the 2D view can fill up the 2D tile cache. Usually the system will clear its cache automatically, but in rare cases, when the cache is near full, the system will spend too much time managing the cache relative to loading tiles, and performance will suffer. When noticing a slowdown in tile loading, the user can press "Clear Cache", and this may alleviate the issue.
{{% /alert %}}


## More Horta 3D features

### Right-click operations

Horta 3D has a number of operations on its right-click menu. As with Horta 2D, some appear only when you right-click over an annotation, and others appear all the time. The options that always appear relate to display, and they are described below in "Display options". 

The operations that pertain the the annotation (anchor) or neuron under the mouse pointer replicate the corresponding functions in Horta 2D's UI (see above).

### Display options

There are a number of view options that can be adjusted in Horta. Many of them are located on the right-click menu under "View".

#### View > Projection

- Maximum Intensity Projection (MIP) is the default display projection mode in Horta. MIP projection provides a robust, informative view of volumetric imagery data, under a wide variety of brightness settings. 
- Occluding projection ("true" volume rendering) can convey a better sense of relative depth, compared to MIP, which can be useful for discriminating neurites that approach one another closely in space. The downside of this superior display quality is that it is very sensitive to the exact brightness settings. The user should probably adjust the brightness settings (Window->Horta->Brightness), especially the minimum intensity value, before switching to the "Occluding" mode.

#### View > Rendering Filter

Horta provides three sub-voxel filtering options: Nearest Neighbor, Trilinear, and Tricubic. These filtering options can be used to trade off speed for image quality. 

- Nearest-neighbor filtering shows the individual voxels of the raw image tile as discrete blocks. This mode is not particularly useful for visualization, but it is fast, and it is very useful for debugging, and for directly demonstrating the true spatial resolution of the underlying imagery data. 
- Trilinear filtering, the default option, interpolates between adjacent voxel intensities using a fast hardware-accelerated computation. This is a good compromise between render speed and image quality. 
- Tricubic interpolation is significantly slower than trilinear interpolation, but yields a smoother appearance, which can make it easier to see the true structure of the tissue being observed.

#### Other View options

- View > Stereo3D lets you choose between multiple methods of displaying images if you have appropriate 3D hardware support. 
- View > Compress voxels in z: This option, when checked, compresses voxels in the z direction to make them appear more cubic. Normally, the data has a worse resolution in z, giving the image volume a stretched look. The user may choose which appearance they prefer. 
- View > Save viewer settings: If you make changes to the color sliders, tracing channel settings, or any other Horta view settings, you must choose this command to save those changes. 
    + **Note**: unlike Horta 2D color model settings, which are saved with the workspace, these settings are save on a per-user basis! If you switch to another workspace, the same Horta color settings will be used. 
- Tracing channel: As described in Horta Basic operation, annotation in Horta 3D is assisted by Horta 3D's "Snap to Signal" feature. This menu allows the user to choose which channel or combination of channels will be used to determine signal brightness.

#### Tiles

The tiles submenu controls loading of the 3D data tiles. Note that in normal operation, tiles are loaded automatically as you navigate through the volume, typically displaying 5-8 tiles surrounding the last mouse click.

- Tiles > Load Horta Tile at Cursor/Focus: Force load a tile at either the center of the screen (Focus) or the location of the mouse cursor; occasionally useful if the automatic algorithm isn't doing what you want. 
- Tiles > Prefer rendered ktx tiles: When checked, Horta will prefer to load the ktx file format tiles. This is the default; ktx tiles load much faster than the alternative raw tiles. If this setting is unchecked, raw tiles, if available, will be loaded instead. Note that you must save viewer settings and restart the application if you change this setting. 
- Tiles > Clear all volume blocks: Choosing this operation forces all tiles to be discarded from the screen and cache and reloaded. 
- Auto-load image tiles: When checked, tiles are loaded automatically as you navigate; when unchecked, you must manually specify load of each desired tile using the other operations (Load at Cursor or Focus).

Normally the user will not change any of these options or use any of these operations.

#### Horta 3D auxiliary windows

Horta 3D has a number of panels containing a variety of supplemental controls. They can be opened from the Window menu > Horta. All of them can be undocked, moved, and redocked anywhere you like. 

- Horta 2D, Horta 3D: opens the view window but does not load any data (if data has already been loaded, it will be visible) 
- Camera Control: provides another way to control the camera in Horta 3D
    + Center focus, rotation, and zoom: replicate mouse commands 
    + View Slab: adjusts how thick the visible slab of data is; the default preset II shows a nice chunk of data; preset I shows a very thin slab, closely resembling the 2D view 
- Color Sliders: used to adjust the color settings for the image data channels and the tracing channel (as described in Horta Basic operation 
- Movie Maker and Scene Editor: not supported or documented
- Graphics Speed: used for debugging; displays some graphics performance data



