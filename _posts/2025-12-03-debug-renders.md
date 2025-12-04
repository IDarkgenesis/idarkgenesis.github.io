---
layout: post
title: Debug renders
date: 2025-12-03 17:57 +0100
categories: [Projects, Sobrassada Engine]
tags: [c++, engine, opengl, rendering] 
---

![DebugRenderImage](/assets/images/SobrassadaEngine/debugRender.png)
 _Debug render menu_

The goal of this task was to create a system that allows adding various render debug options, such as visualizing the static and dynamic trees to identify frustum culling issues, rendering the AABB and OBB of game objects, and providing an unlit render when there are no lights in the scene.

To simplify the management of this information, I used C++'s ``bitset`` class to store the currently active debug modes. The size of the ``bitset`` is determined by a ``constexpr`` array of ``char*`` containing the display strings for the menu, allowing easy scalability for additional debug modes.

[DebugDrawModule](https://github.com/TheCenturiaGames/Sobrassada_Engine/blob/main/SobrassadaEngine/Modules/DebugDrawModule.h) in engine repository.

```cpp
enum class DebugOptions : uint8_t
{
    RENDER_LIGTHS = 0,
    RENDER_WIREFRAME,
    RENDER_AABB,
    RENDER_OBB,
    RENDER_STATICTREE,
    RENDER_DYNAMICTREE,
    RENDER_CAMERA_RAY,
    RENDER_NAVMESH,
    RENDER_PHYSICS_WORLD,
    RENDER_GBUFFERS,
    RENDER_DEPTH,
    RENDER_SHADOWMAP,
    RENDER_NAVMESH_MESHES,
    RENDER_SPLINES,
    RENDER_DEBUG_VISUALS,
    RENDER_SSAO,
};

constexpr const char* DebugStrings[] = {"Render Lights",  "Render Wireframe", "AABB",          "OBB",
                                        "Static Tree",    "Dynamic Tree",     "Camera Ray",    "Navmesh",
                                        "Physics World",  "GBuffers",         "Depth",         "ShadowMap",
                                        "Navmesh Meshes", "Splines",          "Debug Visuals", "SSAO"};

class DebugDrawModule : public Module
{
  public:
  ...
  private:
    void HandleDebugRenderOptions();

  private:
    static DDRenderInterfaceCoreGL* implementation;
    std::bitset<(sizeof(DebugStrings) / sizeof(char*))> debugOptionValues;
};                            
```

