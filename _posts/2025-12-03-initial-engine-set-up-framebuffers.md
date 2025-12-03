---
layout: post
title: Initial engine set-up & framebuffers
date: 2025-12-03 09:56 +0100
categories: [Projects, Sobrassada Engine]
---

The primary goal was to develop the core engine project, incorporating feedback from the second assignment. To support multiple viewports in the engine and facilitate future rendering effects and techniques, we needed to implement framebuffers.

``Framebuffers`` can be configured with various textures, including multiple color textures, as well as depth and stencil attachments. This setup enables us to perform multiple rendering passes without overwriting the final result. Additionally, the framebuffer resize check is handled during the engine's post-update phase, ensuring that there are no issues with the current frame being rendered.

[Link](https://github.com/TheCenturiaGames/Sobrassada_Engine/blob/main/SobrassadaEngine/Utils/Framebuffer.h) to the framebuffer class in the engine repository.

![img-description](/assets/images/SobrassadaEngine/engine.png)
_Engine view_
