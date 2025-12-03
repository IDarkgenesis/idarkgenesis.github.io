---
layout: post
title: Delegates & Event dispatchers
date: 2025-12-03 17:06 +0100
categories: [Projects, Sobrassada Engine]
---

This task focused on creating a system to **store** engine **class functions without** requiring a **direct reference** to the class. The goal was to be able to store multiple function calls and dispatch them when needed.

To accomplish this, we implemented the `Delegate` class, which utilizes C++ templates and the `functional` library. One of the key use cases where this approach proved valuable was in handling the **on-collision events** of the **collider components**, allowing for flexible and decoupled event handling.

Delegate class:
```cpp
#pragma once

#include <functional>

template <typename ReturnValue, typename... Arguments> class Delegate
{
  public:
    Delegate() = default;

    Delegate(std::function<ReturnValue(Arguments...)>& newCallback) { callback = newCallback; };

    Delegate(std::function<ReturnValue(Arguments...)>&& newCallback) { callback = std::move(newCallback); }

    ~Delegate() = default;

    ReturnValue Call(Arguments... args) { return callback(args...); }

  private:
    std::function<ReturnValue(Arguments...)> callback;
};
```

Event dispatcher class:

```cpp
#pragma once

#include "Delegate.h"
#include <list>

template <typename ReturnValue, typename... Arguments> class EventDispatcher
{
  public:
    EventDispatcher() = default;
    ~EventDispatcher() { subscribedCallbacks.clear(); };

    auto SubscribeCallback(Delegate<ReturnValue, Arguments...>&& delegate)
    {
        return subscribedCallbacks.insert(subscribedCallbacks.end(), std::move(delegate));
    };

    void RemoveCallback(typename std::list<Delegate<ReturnValue, Arguments...>>::iterator delegatePosition)
    {
        subscribedCallbacks.erase(delegatePosition);
    }

    void Call(Arguments... args)
    {
        for (auto& callback : subscribedCallbacks)
        {
            callback.Call(args...);
        }
    };

  private:
    std::list<Delegate<ReturnValue, Arguments...>> subscribedCallbacks;
};

template <> class EventDispatcher<void>
{
  public:
    EventDispatcher() = default;
    ~EventDispatcher() { subscribedCallbacks.clear(); };

    auto SubscribeCallback(Delegate<void>&& delegate)
    {
        return subscribedCallbacks.insert(subscribedCallbacks.end(), std::move(delegate));
    };

    void RemoveCallback(std::list<Delegate<void>>::iterator delegatePosition)
    {
        subscribedCallbacks.erase(delegatePosition);
    }

    void Call()
    {
        for (auto& callback : subscribedCallbacks)
        {
            callback.Call();
        }
    };

  private:
    std::list<Delegate<void>> subscribedCallbacks;
};
```
