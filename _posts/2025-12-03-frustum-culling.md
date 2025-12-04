---
layout: post
title: Frustum culling
date: 2025-12-03 16:28 +0100
categories: [Projects, Sobrassada Engine]
tags: [c++, engine, rendering]
---

The goal of this task was to implement frustum culling for rendering objects, ensuring that only the objects within the camera's frustum/view are rendered.

To achieve this, I implemented ``quadtree`` and ``octree`` data structures to store references to the game objects within the scene. However, due to the nature of our game, which incorporates height and verticality, we ultimately decided to use only octrees, as they better accommodate 3D spatial partitioning.

To avoid creating separate functions for each type of object that could intersect with the tree, I used templates, making the system more flexible and reusable.

[Octree](https://github.com/TheCenturiaGames/Sobrassada_Engine/blob/main/SobrassadaEngine/Utils/Trees/Octree.h) in engine repository.

![img-description](/assets/images/SobrassadaEngine/frustumCulling_2.png)
_No frustum culling_

![img-description](/assets/images/SobrassadaEngine/frustumCulling_1.png)
_Active frustum culling_
