---
layout: post
title: Tuple for component storage
date: 2025-12-03 20:35 +0100
categories: [Projects, Sobrassada Engine]
---

Game object components use **polymorphism** and were originally stored in a map, with an **enum** representing their type and a `Component*`. The problem with this approach was that making specific calls to retrieve the correct component pointer required **dynamic casting**, which introduced **performance overhead**. To eliminate this overhead, I changed the storage for components to use **tuples** instead.



Section of [ComponentUtils](https://github.com/TheCenturiaGames/Sobrassada_Engine/blob/main/SobrassadaEngine/Scene/Components/ComponentUtils.h):

```cpp
...
class ComponentUtils
{
  public:
    static void CreateEmptyComponent(ComponentType type, UID uid, GameObject* parent);

    static void CreateExistingComponent(const rapidjson::Value& initialState, GameObject* parent);
};

#define COMPONENTS                                                                                                     \
    MeshComponent*, PointLightComponent*, SpotLightComponent*, DirectionalLightComponent*,                             \
        CharacterControllerComponent*, Transform2DComponent*, CanvasComponent*, UILabelComponent*, CameraComponent*,   \
        ScriptComponent*, CubeColliderComponent*, SphereColliderComponent*, CapsuleColliderComponent*,                 \
        AnimationComponent*, AIAgentComponent*, ImageComponent*, ButtonComponent*, AudioSourceComponent*,              \
        AudioListenerComponent*, BillboardComponent*, CanvasScalerComponent*, SplineComponent*, DecalComponent*,       \
        TrailComponent*, ParticleSystemComponent*, ShaderScriptComponent*, VideoComponent*, VolumetricAreaComponent*

#define COMPONENTS_NULLPTR                                                                                             \
    nullptr, nullptr, nullptr, nullptr, nullptr, nullptr, nullptr, nullptr, nullptr, nullptr, nullptr, nullptr,        \
        nullptr, nullptr, nullptr, nullptr, nullptr, nullptr, nullptr, nullptr, nullptr, nullptr, nullptr, nullptr,    \
        nullptr, nullptr, nullptr, nullptr
```

Section of [GameObject](https://github.com/TheCenturiaGames/Sobrassada_Engine/blob/main/SobrassadaEngine/Scene/GameObjects/GameObject.h) header file:

```cpp
class SOBRASADA_API_ENGINE GameObject
{
  public:
  ...
  template <typename T> T GetComponent() const { return std::get<T>(compTuple); }
  ...
  private:
  ...
    std::tuple<COMPONENTS> compTuple     = std::make_tuple(COMPONENTS_NULLPTR);
    std::bitset<std::tuple_size<decltype(compTuple)>::value> createdComponents;
}
```

By using **tuples**, **dynamic casting** is no longer needed when accessing components, improving **performance**. To iterate through the tuples, I created **template functions**.


Example of code for generating tuple iteration in the [GameObject](https://github.com/TheCenturiaGames/Sobrassada_Engine/blob/main/SobrassadaEngine/Scene/GameObjects/GameObject.cpp) cpp file:

```cpp
// DUPLICATE COMPONENTS
template <std::size_t I = 0, typename... Tp>
inline typename std::enable_if<I == sizeof...(Tp), void>::type
DuplicateComponents(std::tuple<Tp...>& tDuplicate, std::tuple<Tp...>& tOriginal)
{
}

template <std::size_t I = 0, typename... Tp>
    inline typename std::enable_if <
    I<sizeof...(Tp), void>::type DuplicateComponents(std::tuple<Tp...>& tDuplicate, std::tuple<Tp...>& tOriginal)
{
    if (std::get<I>(tOriginal))
    {
        std::get<I>(tDuplicate)->Clone(std::get<I>(tOriginal));
    }
    DuplicateComponents<I + 1, Tp...>(tDuplicate, tOriginal);
}
```
