---
layout: post
title: Raycasting & object picking
date: 2025-12-03 16:44 +0100
categories: [Projects, Sobrassada Engine]
---

![Object Picking](/assets/images/SobrassadaEngine/objectPicking.gif)

The goal of this task was to enable the **selection of objects** placed in the scene, making it easier to design levels and modify game objects.

To achieve this, we **cast a ray in world space**, originating from the user's *click position* on the window and using the **camera matrix** to define its direction.

Next, we **query** the **game objects within** the **tree nodes** that the ray intersects. **For each** object whose **AABB** (Axis-Aligned Bounding Box) **collides** with the ray, we **transform the ray** into the **object’s local space**. The ray is then tested against each triangle in the object’s mesh, keeping track of the closest intersection and returning the selected game object.

To handle raycasting, we created a ``Raycast controller`` using templates. In later tasks, raycasting to the physics world was also integrated.

```cpp
namespace math
{
    class LineSegment;
}

class GameObject;
struct BulletUserPointer;

namespace RaycastController
{
    SOBRASADA_API_ENGINE GameObject*
    GetRayIntersectionObject(const math::LineSegment& ray, const std::vector<GameObject*>& queriedGameObjects);

    SOBRASADA_API_ENGINE BulletUserPointer* GetRayIntersectionPhysics(const math::LineSegment& ray);

    template <typename... Tree> GameObject* GetRayIntersectionTrees(const math::LineSegment& ray, const Tree*... trees)
    {
        std::vector<GameObject*> queriedGameObjects;
        (trees->template QueryElements<math::LineSegment>(ray, queriedGameObjects), ...);

        return GetRayIntersectionObject(ray, queriedGameObjects);
    }
}
```

[Implementation](https://github.com/TheCenturiaGames/Sobrassada_Engine/blob/main/SobrassadaEngine/Utils/RaycastController.cpp) of the class function.
