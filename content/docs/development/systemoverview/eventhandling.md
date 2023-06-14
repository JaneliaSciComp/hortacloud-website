---
title: "Event Handling/Communication"
linkTitle: "Event Handling/Communication"
weight: 5
description: >
    How information is communicated in Horta
---

This section briefly covers how information is shared throughout the GUI in Horta


### EventBus

To preserve code independence between different modules in Horta, an EventBus from Google is used.  This acts likes an internal publish/subscribe framework within the client, allowing methods to listen for certain events to happen.  Events are sent asynchronously on the EventBus, so they are managed in a separate thread than the main AWT thread.  

In Horta, the EventBus is mainly used to communicate events that concern multiple GUI components.  Events that are mostly concerned with communication within a GUI component are still managed using Listeners.
- `ViewerEventBus is the main wrapper around the eventbus for Horta, and is managed by TmViewerManager.`
- `Each main component has a Controller class that is the main hub for incoming eventbus messages (eg, PanelController for the Horta Command Center).  Messages are then appropriately routed to local GUI components from the controller.

All the eventbus events are listed in the org.janelia.workstation.controller.eventbus package and include events such as SampleCreateEvent, WorkflowEvent, etc.  Because events can have inheritance, methods can listen to the super Event class and get notifications on the whole stack of events that inherit from the super class.


