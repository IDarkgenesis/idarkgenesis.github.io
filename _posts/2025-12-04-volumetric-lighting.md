---
layout: post
title: Volumetric lighting
date: 2025-12-04 10:20 +0100
categories: [Projects, Sobrassada Engine]
tags: [engine, opengl, rendering]
---

![interactingVolumetric](/assets/images/SobrassadaEngine/dynamicVolumetric2.gif)

**Volumetrics** simulate the scattering of light within an environment. This feature includes several optimizations to enhance both the **visual quality** and **customizability** of the effect.

First, we implemented **spotlight shadow maps**, which serve as the foundation for determining whether a fragment in the scene is visible from the spotlight’s perspective. Next, we created a **compute shader** responsible for calculating the **volumetric lighting** itself.

To further improve rendering quality, we employed a technique called **dithering**, which involves offsetting the starting position of the ray. This helps minimize **artifacts** in the final render, which can appear as noticeable light banding or abrupt transitions in the lighting.

![volumetrics1](/assets/images/SobrassadaEngine/volumetricArtifacts.png)
 _Artifacts that can appear if not using dithering_

Additionally, we render the volumetrics to a texture that is **half the size of the screen**. This reduces both the **noise** in the result and the time spent on calculations. Afterward, we apply a **Gaussian filter** to further smooth out any remaining noise.

For even greater control over the appearance of the volumetrics, each spotlight can have a custom **anisotropy** value. This allows for further **customization** of how the light is scattered, giving more flexibility in adjusting the look of the rendered volumetrics.

![volumetrics2](/assets/images/SobrassadaEngine/volumetrics2.png)
 _Example of volumetrics inside the game_

Another key **performance optimization** is that we calculate the intersection of the **camera ray** with the **light cones**. By doing this, we can limit the volumetric lighting calculations to only the portion of the scene within the start and finish points of the ray’s intersection, significantly improving performance by reducing unnecessary computations outside of the relevant area.

Other optimizations include calculating an **AABB** (Axis-Aligned Bounding Box) for the spotlight frustum and using it to **cull** volumetrics that are not visible to the camera. Lastly, we optimized spotlight shadow map rendering by rendering it **only once** for certain spotlights. Since some spotlights don’t interact with moving objects, we can render the shadow map on the first frame and reuse it for subsequent frames.
