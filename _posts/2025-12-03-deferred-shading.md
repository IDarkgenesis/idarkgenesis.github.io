---
layout: post
title: Deferred shading
date: 2025-12-03 18:53 +0100
categories: [Projects, Sobrassada Engine]
---

**Deferred shading** is a rendering technique that optimizes the process by first rendering all texture and material data into separate buffers, and then performing a single lighting pass using the information from those buffers.

In this task, I created the [G-Buffer](https://github.com/TheCenturiaGames/Sobrassada_Engine/blob/main/SobrassadaEngine/Utils/GBuffer.cpp) class. This buffer contains a texture for each piece of data required for **lighting calculations**, including **diffuse, specular, position, normals, and emissive** values. Additionally, I developed two new shaders to handle the rendering pipeline: the first shader renders all necessary data to the **G-Buffer**, while the second handles the lighting pass using the textures stored in the **G-Buffer**.

```cpp
#pragma once

class GBuffer
{
  public:
    GBuffer(int width, int height);
    ~GBuffer();

    void Bind();
    void Unbind();
    void Resize(int width, int height);
    void CheckResize();
    unsigned int GetDepthTexture() const { return depthTexture; }

    int GetScreenWidth() const { return screenWidth; }
    int GetScreenHeight() const { return screenHeight; }

  private:
    void InitializeGBuffer();

  public:
    unsigned int gBufferObject   = 0;

    unsigned int diffuseTexture  = 0;
    unsigned int specularTexture = 0;
    unsigned int positionTexture = 0;
    unsigned int normalTexture   = 0;
    unsigned int emissiveTexture = 0;

  private:
    bool shouldResize                = false;
    int screenHeight                 = 0;
    int screenWidth                  = 0;

    unsigned int depthTexture        = 0;
    unsigned int colorAttachments[5] = {};
};
```

The **G-Buffer** is also used for other **rendering effects** and **post-processing**, allowing for efficient use of the data in tasks such as screen space reflections, ambient occlusion, and other advanced effects.

To further optimize the rendering process, I implemented a **stencil test** to ensure that the scene is only rendered where necessary, preventing unnecessary rendering on top of the skybox.

![GBufferDebug](/assets/images/SobrassadaEngine/gbufferRender.png)
 _G-Buffer debug render_
