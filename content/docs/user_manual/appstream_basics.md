---
title: "AppStream Basics"
linkTitle: "AppStream Basics"
weight: 20
description: >
    Some general information about running HortaCloud in the AppStream environment
---


## The AppStream environment

In general, the AppStream environment behaves like a Windows desktop computer running a single application, Horta. Because it's running in a browser, though, there are some important differences. Many of them are managed via the AppStream toolbar at the top of the screen.  

Older AppStream toolbar:

{{< imglink src="../AppStream-toolbar.png" link="../AppStream-toolbar.png" alt="AppStream 1 toolbar" width="761px" >}}

Newer AppStream toolbar:

{{< imglink src="../AppStream2-toolbar.png" link="../AppStream2-toolbar.png" alt="AppStream 2 toolbar" width="1076px" >}}

### Data import and export: file system, cloud storage, and clipboard

**Note**: The AppStream toolbar may differ slightly; the icon positions referred to below are for the older AppStream version. 

Web browsers are constrained with respect to how they read and write data on a computer, and applications running in virtual machines displayed in a browser, even more so.

- File system access: The third icon from the left opens a dialog showing "My Files", and within it, a "Temporary Files" location. This is a file location on the remote computer running Horta and Horta can read and write to. You can then transfer files from that location to your local computer via the "My Files" dialog box. See "Importing Data" and "Exporting Data" in the "Basic Operations" section for more details.

- Cloud storage: Files can also be transferred to cloud storage. The exact services available will depend on your local deployment. To connect to a cloud storage provider, click the "My Files" icon on the tool boar, then click the "Add Storage" button to connect to an available synchronized folder. Contact your local administrator for details on what cloud storage is available in your instance.

- Clipboard access: If you copy anything in Horta to the clipboard, it's only accessible on the remote computer. The fourth icon from left on the toolbar will transfer the remote clipboard's contents to the local clipboard.

### Windows and screens

Horta users often want to see the 2D and 3D views simultaneously, or they want a maximum 3D view while leaving room for the Data Explorer and/or Horta Control Center. To enable use of multiple monitors, click the seventh icon from left on the toolbar. There is also a full-screen mode enabled via the sixth icon.

The second icon on the toolbar shows all windows, in case you undock a Horta Window and then lose it behind a larger window. There's also a terminal window from which Horta is initially run; this can be ignored. If you close it, it will close Horta.

### Quitting and relaunching Horta

Sometimes Horta does have issues. Often reloading data by reopening a workspace will clear them up. If not, to relaunch the application again without ending the AppStream session, choose File > Exit or click the "x" at top right. Then relaunch the application by clicking the first icon in the toolbar and choosing "Horta Workstation" again.

### Ending the session

Generally sessions will automatically close after a period of inactivity. To end the session manually, you can choose "End Session" from the drop-down menu on the top-right toolbar button.

Remember that when you end a session, some data that is stored on the remote computer will be lost:

Volatile data:
- files in "MyFiles/Temporary Files"
- information on the clipboard

All other data (sample data, workspace data, annotations, notes, etc.) is stored in the database and is not lost when a session ends.

Any data you wish to preserve (e.g., exported neuron SWC files) should be moved from "My Files" to either local storage or cloud storage before you end the session.


### Reconnecting

If your browser window is accidentally closed, your session is not lost. Simply repeat the login steps, and you will be reconnected to the session, even if from a different browser and/or computer.

