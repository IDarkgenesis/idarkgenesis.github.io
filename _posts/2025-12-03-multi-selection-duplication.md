---
layout: post
title: Multi-selection & duplication
date: 2025-12-03 19:52 +0100
categories: [Projects, Sobrassada Engine]
tags: [c++, engine]
---

To improve scene management, I implemented **multiple selection functionality**. By holding **Shift** and **left-clicking** on objects in the scene, the selected game object is added to the selection group. Afterwards, when using the **move**, **rotate**, or **scale gizmos**, all selected objects are manipulated properly. The **mobility settings** (**static**, **dynamic**, **kinematic**) are preserved even after the multi-selection is cleared.

Additionally, I added the option to **duplicate game objects** by selecting one and pressing **Ctrl + D**. If multiple objects are selected, all of them are duplicated while retaining their original **mobility settings**.


![multiSelect](/assets/images/SobrassadaEngine/multiSelect.gif)
