---
layout: post
title: Billboards
date: 2025-12-03 21:05 +0100
categories: [Projects, Sobrassada Engine]
tags: [c++, engine, opengl, rendering] 
---

The main use of **billboards** is to provide an efficient way of rendering multiple planes with **textures**, such as **vegetation**. This also includes the ability to render **sprite sheet animations**, for example, a **fire** or a **dust cloud**. Additionally, **UV coordinates** can be modified to render only a specific part of a texture.

The implementation in the engine utilizes **OpenGL instancing**. Billboards are treated as a **shared resource** for components. When a component requests a billboard, its **position** is added to a list, and then, with a single **draw call**, multiple instances of billboards are rendered.

In the end, billboards were not used for **vegetation rendering**, as we decided to render vegetation as intersections of **plane meshes**. However, the work done with billboards played a crucial role in the development of the **particle system**, where the **instancing technique** proved to be highly beneficial.

![BillboardImage](/assets/images/SobrassadaEngine/billboard.png)  
_**Example of a billboard**_
