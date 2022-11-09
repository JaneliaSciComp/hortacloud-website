---
title: "Reference"
linkTitle: "Reference"
weight: 50
description: >
    Horta reference
---

## Import/export file formats

### SWC

The swc file format is a standard format for recording neuron skeletons. Unfortunately, while the basic format is widely used, there is no single standard for defining the format, and as a result, there are differences in implementation among various swc writers and readers. The basic spec can be seen [here](http://www.neuronland.org/NLMorphologyConverter/MorphologyFormats/SWC/Spec.html).

In summary, an SWC file:

- is a text file
- contains a number of header lines, each beginning with a '#' character
- followed by a list of points, one per line, with parents preceding children

Each line consists of seven numbers separated by whitespace in the following order: `id type x y z radius parent`, with:

| field | value |
| --- | --- |
| id | integer identifying the point, in ascending order |
| type | integer indicating the type of the previous segment, with: <br/> 0 = undefined <br/> 1 = soma <br/> 2 = axon <br/> 3 = dendrite <br/> 4 = apical dendrite <br/> 5 = fork point <br/> 6 = end point <br/> 7 = custom |
| x, y, z | floating point coordinates of the point |
| radius | floating point radius of the previous segment |
| parent | id value of the parent for the current point; a root point has parent = -1 |

Notes:

- two fields are non-critical for rendering the basic neuron skeleton:
  - the `type` field describes but does not affect connectivity and can usually be ignored
  - the `radius` field does not affect connectivity; it's only necessary if you are rendering neuron volumes (skeletons with width)
- a node's `id` must appear in the `id` column before it appears as a `parent`; that is, child nodes must appear lower in the list than parent nodes

#### Janelia-specific details

- Only nodes with `type` 5 and 6 are marked by Horta, as they are the only types that can be inferred from geometry at export time. All other points are marked as `type` 0. On import, the `type` column is ignored.
- Coordinates and radii are assumed to be in microns.
- A few headers are used by Horta:
  - `ORIGINAL_SOURCE`, a fairly standard header, is set to "Janelia Workstation Large Volume Viewer"
    - That is the original name for the software, back when it was only 2D, and we decided not to change this identifier.
  - The x, y, z locations of the nodes are centered on the origin at export time because not all SWC viewers can handle the large coordinates (Horta can). The header field `OFFSET` gives the x, y, z offset needed to reproduce the original annotation location. The values are whitespace-delimited floating point numbers. Horta correctly handles this field during its own import and export; it will always use the field on export, but it doesn't complain if it's missing on import. If an swc reader ignores this header, the neuron will be of correct shape but shifted in space from its original location.
  - The color of the exported neuron (optional) is recorded in the header field `COLOR` as a comma-separated list of floating point RGB values ranging from 0 to 1.
  - Other headers are ignored at import.
- The name of the neuron is not stored inside the swc file. However, when imported, the neuron will be named like the swc file.

Here is an example swc file:

```
# ORIGINAL_SOURCE Janelia Workstation Large Volume Viewer
# OFFSET 76290.282407 42379.443335 23460.277313
# COLOR 0.501961,0.000000,1.000000
1 0 -870.258314 84.790733 0.000000 1.000000 -1
2 0 -408.096941 6.007367 0.000000 1.000000 1
3 5 54.064431 -72.775998 0.000000 1.000000 2
4 0 232.512856 -256.688292 0.000000 1.000000 3
5 6 600.186790 -429.961032 0.000000 1.000000 4
6 0 159.078322 142.548313 0.000000 1.000000 3
7 6 232.512856 526.078910 0.000000 1.000000 6
```

### JSON

When neurons are exported from Horta in swc format, any notes attached to the neuron's points are also exported. The notes are contained in a JSON file of the same name as the swc file but with a ".json" extension. The JSON file contains a dictionary with the following fields:

- workspaceID: contains the integer workspace ID number
- username: contains the workstation username of the user doing the export
- offset: contains a list of floating point numbers [x, y, z] holding the offset as described in the section above
- neurons: contains a list of dictionaries, one per neuron exported; each dictionary has fields:
  - neuronID: contains the integer neuron ID number
  - notes: contains a list of neuron notes; each note is a list of [x, y, z, note]
- on import, the username and workspace and neuron IDs are ignored

The x, y, z, coordinates of each note will match the coordinates of the corresponding annotation in the corresponding swc file, as they use the same offset system. Horta automatically imports and exports the JSON files when the swc files are imported or exported.

Here is an example JSON file that corresponds to the above example swc file:

```
{
  "workspaceID" : 2229358932059488401,
  "username" : "olbrisd",
  "neurons" : [ {
    "neuronID" : 2653026075256291473,
    "notes" : [ [ 232.51285642151197, 526.0789095035507, 3.637978807091713E-12, "traced end" ], [ 232.51285642151197, -256.68829229611583, 3.637978807091713E-12, "interesting" ] ]
  } ],
  "offset" : [ 76290.28240663109, 42379.443335160235, 23460.2773125 ]
}
```

## Image file formats

### Raw tiff stacks

The raw microscope data is stored in tiff stacks. They can be displayed in Horta 2D on demand, but they are typically used only in rare instances when stitching in the rendered images is not perfect. Typically this data is only available in the desktop version of the software at the site the sample was imaged.

Details of the files and their organization will be added later.

### Rendered 2D images

The raw microscope data is transformed and stitched together into a contiguous rendered whole. It's also stored in tiff stacks, rendered at multiple resolutions and arranged in an octree. For HortaCloud, this data likely only has the a single low-resolution image for each channel.

Details of the files and their organization will be added later.

### HortaKTX 3D volumes

Horta's 3D images are optimized for fast loading into the Horta viewer. The HortaKTX format is based on the [KTX file format](https://registry.khronos.org/KTX/specs/1.0/ktxspec_v1.html) which is designed to load directly into graphics cards. In order to keep the files both small enough to load rapidly and small enough to economically store, the rendered data is transformed and down-sampled before KTX tile creation. These files are also arranged in a multiscale octree for full volume viewing.

## A* algorithm for automatically traced paths

The algorithm for computing the path is:

- Extract a rectangular solid block of voxels containing the two endpoints, plus a small buffer region in all three dimensions. (Thus, if the neuron meanders outside of this block, the technique will not work well. Try to choose anchors with relatively straight connections between them.)
- Measure the variation in voxel intensity within that block: record mean and standard deviation.
  - Currently, only the first channel is examined!
- Assume that the background (non-neuron) intensities follow a normal distribution, with the measured parameters (mean and standard deviation).
- Define the path weight for a particular voxel, as the probability that an intensity greater than or equal to the voxel intensity, could have arisen randomly from the background distribution. Thus dim voxels get a large path weight, and bright voxels get a small path weight. This value can get extremely small for very bright pixels (think 10 raised to the minus seventy power). You can think of this weight as (1.0 minus the probability this voxel is a neuron), assuming all voxels are either background or neurons.
  - weight = erf( (intensity - mean) / (standard_deviation) ), where erf() is the [error function](http://en.wikipedia.org/wiki/Error_function).
- Because it uses the mean and standard deviation of intensities, this method of path weighting is robust in the face of large uniform intensity bias offsets, like we sometimes see in the mouse brain imagery.
- Use the [A* (A-star) algorithm](https://en.wikipedia.org/wiki/A*_search_algorithm) to compute the lowest cost path connecting the two endpoints. This path is guaranteed to be optimal.
- The algorithm will be terminated if it takes more than 10 seconds to finish.

## User groups for ownership purposes

There are two user groups in the workstation that are used in neuron ownership operations. They are set at compile time to values specified in the `console.properties` file.

```
console.LVVHorta.tracersgroup=group:mouselight
console.LVVHorta.activetracersgroup=group:mouselight_tracers
```

- The "tracersgroup" variable should be set to a group that contains all of the people using Horta to trace neurons. If a neuron is owned by this group, any member of the group can take ownership immediately. This group is expected to be used for ownership of samples and workspaces as well, so it may contain people who are not tracers but who will be looking at the same data and/or the tracers' work.
- The "activetracersgroup" variable should be set to a group containing only people who are tracing. The members of this group will be listed in some neuron ownership change dialogs for convenience.
- If either group does not exist in the workstation database, the corresponding features will be disabled.
- **Note**: This scheme assumes that all tracers are members of one group that shares data freely. If you need to support multiple groups that will not be sharing data, you should currently deploy separate infrastructure (servers, etc) to support each group.
