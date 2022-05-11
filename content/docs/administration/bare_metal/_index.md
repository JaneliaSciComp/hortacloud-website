---
title: "Bare metal"
linkTitle: "Bare metal"
weight: 20
simple_list: true
description: >
  Learn how to deploy a Horta instance on your own servers
---

The recommended way to deploy HortaCloud is to use [Amazon Web Services (AWS)](aws), but you can also deploy the Horta services locally on your own client/server machines.

Using Docker Swarm, you can choose to deploy on two or three servers. The primary benefit of using three servers is that the MongoDB cluster will have a complete replica set for high availability.
