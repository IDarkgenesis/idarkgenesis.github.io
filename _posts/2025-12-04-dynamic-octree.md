---
layout: post
title: Dynamic octree
date: 2025-12-04 12:06 +0100
categories: [Projects, Sobrassada Engine]
tags: [c++, engine, rendering]
---

In order to improve performance, a new version of the [**Octree**](https://github.com/TheCenturiaGames/Sobrassada_Engine/blob/main/SobrassadaEngine/DynamicOctree.h) was created with the ability to remove objects and merge nodes when possible. To simplify the update process, only one reference of a game object can be present in the tree at any given time. This approach is most effective for dynamic objects that don't occupy large areas of the camera's view.

The main idea is to collect a list of game objects whose **transform** has been updated during the current frame. In the **Post Update** phase, the system checks if any of these objects have moved away from their parent node. If they have, the objects are removed from the tree, and the tree is merged if necessary. The objects are then reintroduced into the tree.

The code for regenerating the octree is entirely **iterative**, which helps avoid filling the call stack with recursive calls.

Code for removing and checking if mergeable:

```cpp
bool DynamicOctree::RemoveElement(GameObject* gameObject, bool goTransformed)
{
    if (gameObject == nullptr) return false;

    bool removed = false;
    std::map<DynamicOctreeNode*, std::pair<bool, int>> nodeStates;

    DynamicOctreeNode* currentNode     = nullptr;

    const AABB objectBoundingBox       = gameObject->GetGlobalAABB();
    DynamicOctreeElement octreeElement = DynamicOctreeElement(objectBoundingBox, gameObject);

    if (goTransformed)
    {
        currentNode = gameObject->GetDynamicNode();
        if (currentNode && currentNode->RemoveElement(octreeElement))
        {
            removed = true;
            --totalElements;
            if (currentNode->elements.size() <= currentNode->elementsCapacity)
                nodeStates.insert({currentNode, std::make_pair(true, (int)currentNode->elements.size())});
            else nodeStates.insert({currentNode, std::make_pair(false, (int)currentNode->elements.size())});
        }
    }
    else
    {
        std::stack<DynamicOctreeNode*> nodesToVisit;
        nodesToVisit.push(rootNode);

        while (!nodesToVisit.empty() && !removed)
        {
            currentNode = nodesToVisit.top();
            nodesToVisit.pop();

            if (currentNode->Intersects(objectBoundingBox))
            {
                if (currentNode->IsLeaf())
                {
                    if (currentNode->HasElement(octreeElement))
                    {
                        --totalElements;
                        removed = currentNode->RemoveElement(octreeElement);

                        // SAVE CURRENT STATE TO LATER CHECK IF MERGEABLE
                        if (currentNode->elements.size() <= currentNode->elementsCapacity)
                            nodeStates.insert({currentNode, std::make_pair(true, (int)currentNode->elements.size())});
                        else nodeStates.insert({currentNode, std::make_pair(false, (int)currentNode->elements.size())});
                    }
                    else
                    {
                        if (currentNode->elements.size() <= currentNode->elementsCapacity)
                            nodeStates.insert({currentNode, std::make_pair(true, (int)currentNode->elements.size())});
                        else nodeStates.insert({currentNode, std::make_pair(false, (int)currentNode->elements.size())});
                    }
                }
                else
                {
                    for (auto child : currentNode->children)
                        nodesToVisit.push(child);
                }
            }
        }
    }

    // CHECK IF MERGEABLE
    auto nodeIterator = nodeStates.find(currentNode);
    if (nodeIterator != nodeStates.end() && nodeIterator->second.first && nodeIterator->first->parentNode)
    {
        std::stack<DynamicOctreeNode*> nodesToCheck;
        std::set<DynamicOctreeNode*> nodesInStack;
        nodesToCheck.push(nodeIterator->first->parentNode);
        nodesInStack.insert(nodeIterator->first->parentNode);

        while (!nodesToCheck.empty())
        {
            currentNode = nodesToCheck.top();
            nodesToCheck.pop();
            nodesInStack.erase(currentNode);

            // CHECK IF IT HAS NODE STATE
            auto nodeStateIterator = nodeStates.find(currentNode);

            // NO STATE FOUND -> UNPROCESSED
            if (nodeStateIterator == nodeStates.end())
            {
                // IF LEAF CHECK AND ADD STATE, ADD PARENT IF NOT ALREADY PRESENT
                if (currentNode->IsLeaf())
                {
                    if (currentNode->elements.size() <= currentNode->elementsCapacity)
                        nodeStates.insert({currentNode, std::make_pair(true, (int)currentNode->elements.size())});
                    else nodeStates.insert({currentNode, std::make_pair(false, (int)currentNode->elements.size())});

                    if (currentNode->parentNode && nodesInStack.find(currentNode->parentNode) == nodesInStack.end())
                    {
                        nodesInStack.insert(currentNode->parentNode);
                        nodesToCheck.push(currentNode->parentNode);
                    }
                }
                // IF NOT LEAF CHECK ALL CHILDS, IF ONE NOT MERGEABLE, BREAK LOOP, IF SO KEEP GOING
                // IF ONE CHILD HAS NO STATE ADD PARENT BACK TO STACK AND THEN CHILD TO BE PROCESSED
                else
                {
                    bool allChildsValid = true;
                    int childElements   = 0;

                    for (int i = 0; i < 8; ++i)
                    {
                        auto childStateIterator = nodeStates.find(currentNode->children[i]);

                        // CHILD IS PROCESSED
                        if (childStateIterator != nodeStates.end())
                        {
                            if (!childStateIterator->second.first) return removed;
                            childElements += childStateIterator->second.second;
                        }
                        // CHILD NOT PROCESSED, ADD FIRST CURRENT NODE (PARENT) TO GO BACK TO IT WHEN CHILDS PROCESSED
                        // (ONLY ONCE)
                        else
                        {
                            if (nodesInStack.find(currentNode) == nodesInStack.end())
                            {
                                nodesInStack.insert(currentNode);
                                nodesToCheck.push(currentNode);
                            }
                            if (nodesInStack.find(currentNode->children[i]) == nodesInStack.end())
                            {
                                nodesInStack.insert(currentNode->children[i]);
                                nodesToCheck.push(currentNode->children[i]);
                            }

                            allChildsValid = false;
                        }
                    }

                    if (allChildsValid && childElements <= currentNode->elementsCapacity)
                    {
                        // MERGE!!!!!! -> FIRST ADD ALL CHILD ELEMENTS TO CURRENT NODE, THEN DELETE CHILD, AND CHILDREN
                        // = NULLPTR

                        for (DynamicOctreeNode*& childNode : currentNode->children)
                        {
                            currentNode->elements.insert(
                                currentNode->elements.begin(), childNode->elements.begin(), childNode->elements.end()
                            );

                            delete childNode;

                            childNode = nullptr;
                        }

                        for (auto& element : currentNode->elements)
                            element.gameObject->SetDynamicNode(currentNode);

                        totalLeaf -= 7;

                        // DONT FORGET TO ADD IT TO NODE STATES!!!!!!
                        nodeStates.insert({currentNode, std::make_pair(true, (int)childElements)});

                        if (currentNode != rootNode && nodesInStack.find(currentNode->parentNode) == nodesInStack.end())
                        {
                            nodesInStack.insert(currentNode->parentNode);
                            nodesToCheck.push(currentNode->parentNode);
                        }
                    }
                    else if (allChildsValid && childElements > currentNode->elementsCapacity) return removed;
                }
            }
            else
            {
                // STILL MERGEABLE -> ADD PARENT TO CONTINUE ITERATION, IF NOT ROOT NODE
                if (nodeStateIterator->second.first)
                {
                    if (currentNode != rootNode && nodesInStack.find(currentNode->parentNode) == nodesInStack.end())
                    {
                        nodesInStack.insert(currentNode->parentNode);
                        nodesToCheck.push(currentNode->parentNode);
                    }
                }
                else return removed;
            }
        }
    }

    return removed;
}
```
