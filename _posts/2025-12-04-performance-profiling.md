---
layout: post
title: Performance profiling
date: 2025-12-04 12:11 +0100
categories: [Projects, Sobrassada Engine]
tags: [c++, engine] 
---

This task was performed throughout the entire project. It involved profiling the performance using **Optick** and **Nvidia NSight** to identify bottlenecks or potential issues that could be resolved, ultimately improving the engine's performance.

![optick](/assets/images/SobrassadaEngine/optick.png)
_Optick capture_

Some of the solved issues:

- **Animation bone transform** being propagated for each bone. This problem meant that a bone would be updated N times, where N is the number of bones above it in the hierarchy.
- Changed all string comparisons to use **StringHashes**.
- **Debug render lines** overflowed even when debug mode was not enabled. It would start printing warning messages for each line that overflowed the buffer.
- [**Update culling**]({% post_url 2025-12-04-game-object-update-culling %}).
- [**Volumetric optimizations**]({% post_url 2025-12-04-volumetric-lighting %}).
- [**Dynamic octree**]( {% post_url 2025-12-04-dynamic-octree %}).

![nsight1](/assets/images/SobrassadaEngine/nsight1.png)
_NSight capture_

![nsight2](/assets/images/SobrassadaEngine/nsight2.png)
_NSight capture_
