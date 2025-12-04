---
layout: post
title: Custom shader support
date: 2025-12-04 11:36 +0100
categories: [Projects, Sobrassada Engine]
tags: [c++, engine, opengl, rendering]
---

This task involved creating a module and a method for using custom shaders on game objects. To achieve this, the system was built on top of the engine's [**Scripting system**](https://github.com/TheCenturiaGames/Sobrassada_Engine/blob/main/SobrassadaEngine/Modules/ScriptModule.h).

I added a new module, the [**Shader Script Module**](https://github.com/TheCenturiaGames/Sobrassada_Engine/blob/main/SobrassadaEngine/Modules/ShaderScriptModule.h), which manages different types of shader scripts based on their **render step** in the rendering pipeline. 

There are six types of shaders:

- **Opaque**: Rendered in the GBuffer.
- **Transparent**: Rendered in the transparent pass.
- **Post lighting**: Rendered after the lighting, but before particles and post effects.
- **Post effects**: Last rendering step before UI.
- **UI**: Rendered after the entire scene has been rendered.

![shaderScriptComponent](/assets/images/SobrassadaEngine/shaderScriptComp.png)
_[Shader script component](https://github.com/TheCenturiaGames/Sobrassada_Engine/blob/main/SobrassadaEngine/Scene/Components/ShaderScriptComponent.h) with different shader scripts attached_

A function in the [**Shader module**](https://github.com/TheCenturiaGames/Sobrassada_Engine/blob/main/SobrassadaEngine/Modules/ShaderModule.h) was created to manage shader compilation. Since shaders are requested from scripts, it's more efficient to compile a shader only once and then return the OpenGL index to the shader if it has already been compiled.

```cpp
#pragma once

#include "HashString.h"
#include "Module.h"

#include <map>

class ShaderModule : public Module
{
  public:
    ...
    unsigned int SOBRASADA_API_ENGINE RequestShaderProgram(const char* vertexPath, const char* fragmentPath);
    ...
  private:
    ...
    std::map<HashString, unsigned int> customShaderPrograms;
}
```
