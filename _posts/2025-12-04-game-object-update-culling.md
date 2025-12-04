---
layout: post
title: Game object update culling
date: 2025-12-04 10:05 +0100
categories: [Projects, Sobrassada Engine]
tags: [c++, engine, rendering]
---

One significant **performance issue** we encountered was that all **game objects** in the scene were updated during gameplay, including those far from the **player character**. This resulted in unnecessary updates to **animation transforms**, even for objects well outside the visible area.

To address this, we implemented **frustum culling** to selectively update only the game objects within the **camera’s view**. However, this approach introduced a new challenge: during certain combat scenarios, if an **enemy** moved out of the camera’s frustum, it would stop being updated, potentially causing **bugs** or **undesired behaviors**.

 ![performanceDiff](/assets/images/SobrassadaEngine/performanceCompCulling.png)
 _Performance difference with those changes applied_

To resolve this, we introduced a **Tag system**, combined with `OnCollisionEnter` and `OnCollisionExit` events for **colliders** and **triggers**. This allowed us to create **trigger zones** within the scene, where each zone is assigned a specific tag. When the **player character** enters a trigger area, all game objects with that tag are updated and rendered, even if they fall outside the camera’s frustum.

![tags](/assets/images/SobrassadaEngine/tags.png)
 _Tags menu for game objects_

