---
layout: post
title: Particle systems
date: 2025-12-03 21:10 +0100
categories: [Projects, Sobrassada Engine]
tags: [c++, engine, opengl, rendering]
---

![particleSystems](/assets/images/SobrassadaEngine/firePS.gif)

[**Particle systems**](https://github.com/TheCenturiaGames/Sobrassada_Engine/blob/main/SobrassadaEngine/Utils/Particles/ParticleSystem.h) also make use of **OpenGL** for **instancing**, and similar to billboards, they are a **shared resource** between [**Particle System components**](https://github.com/TheCenturiaGames/Sobrassada_Engine/blob/main/SobrassadaEngine/Scene/Components/ParticleSystemComponent.h). Thanks to instancing, the number of **draw calls** for each resource-based particle system is determined by the number of **unique emitters**, not the number of instances of particles, optimizing **performance**. This feature has been constantly **iterated upon** throughout the project, with fixes and new features added based on feedback from artists using it.

Particle systems are composed of [**emitters**](https://github.com/TheCenturiaGames/Sobrassada_Engine/blob/main/SobrassadaEngine/Utils/Particles/ParticleEmitter.h), with each emitter using a **texture** or **material** as the base for a particle. To modify the properties of these particles, each emitter can have a set of **add-ons**.

The available add-ons are:

## **Base Add-on**

This add-on is common to all emitters and provides the most **basic particle customization** options. It contains the following customizable settings:

- **Looping**
- **Duration**
- **Emitting range**
- **Particle count**
- **Spawn all particles at once**
- **Rotation**: Can be randomized
- **Lifetime**: Can be randomized
- **Size**: Can be randomized, or use **Bezier curves** for interpolation or a **custom curve editor**.

![BaseAddon](/assets/images/SobrassadaEngine/baseAddon.png)
_Base addon settings_

## **Velocity Add-on**

This add-on is required if the user wants to apply **movement** to particles. The **velocity** can be customized along the **X**, **Y**, and **Z** coordinates. The values can be randomized between two ranges, interpolated using a **Bezier curve**, or controlled through a **custom curve editor**.

## **Spritesheet Add-on**

If the selected resource to be rendered as a particle is a **sprite sheet**, this add-on allows the user to input the number of **rows** and **columns** of the sprite sheet, as well as the **animation speed**.

## **Color Add-on**

The **Color Add-on** adds a **gradient** and **color widget**, allowing the user to configure both the **color** and **alpha value** of the particle over its **lifetime**.

![ColorAddon](/assets/images/SobrassadaEngine/colorAddon.png)
_Color addon settings_

## **Area Add-on**

The **Area Add-on** allows particles to spawn at locations different from the emitter’s position. It supports various **shapes** that control the spawn area and direction of the particles.

### Available shapes:

- **Cube**: Particles can spawn inside the volume or surface.
- **Circle**: Particles can spawn on the surface.
- **Sphere**: Particles can spawn inside the volume or surface. 
- **Cone**: Particles spawn inside the volume.

![areaAddon](/assets/images/SobrassadaEngine/areaAddon.png)
_Color addon settings_
