---
layout: post
title:  "Backend of our node system."
date:   2021-04-11 21:21:21 +0200
categories: project7 update
author: Tommi
---

Summary  
-----------
We needed a node-scripting system according to our course.  
There was one we got from the school, but it suffered from some annoyances.  
* Pins are void* to data, any data needs to be allocated with new/delete
* Virtual functions for execution, easy to use, extra dereferences most of the time
* Pins need to be referenced by their numbers
* Scripts have lots of different std::vectors and std::map/unordered_maps at runtime
* Pins can contain any type, pretty hard to debug
* No way to track back the call order of the nodes
but I wanted to do better  

These were my goals  
-----------
1. Pins are in-place, no separate allocations.  
2. No virtual functions  
3. Named pins, no need to remember pin0 is a string named `name` or `key` etc.  
4. Scripts should contain a single block of memory, no fragmentation  
5. Pins can only contain valid types  
6. Some way of tracking the callstack  

but there's no goal too high!  

The solution
-----------

The plan is to pack all nodes into a chunk of allocated memory. This will solve Goal #1 and #4

Let's take a look at an example node  
![example_node](/images/nodesystem/example_node.png)  
Let's take a look at its contents  
1. 1 input function  
2. 3 input values  
3. 1 output value
4. 1 output function  

Here we all input/output have variable names, so we fulfill Goal #3

The way it works is that `PinRef`, has only a relative memory offset.  
Since we know it's all going to be in the same big chunk of consecutive memory, we just take the pins `this` pointer and adds the memory offset to it.  
It will ultimately point to another node in the chunk, this is managed by a NodeScript factory or similar, I will not go into the construction here, only how it's stored.  
![pinref](/images/nodesystem/pinref.png)  

For `Flow` we do the same thing, but we point to another node instead. We also need to store a function pointer. I know, a function pointer is not optimal, but it's better than a virtual function most of the time, so still abiding Goal #2.  
![flow](/images/nodesystem/flow.png)  
And converting the member function that's the input to a `void(*)(void*)`
![memberfn_to_voidstar](/images/nodesystem/memberfn_to_voidstar.png)  

Another bonus of this is that we get access to C++ callstack and don't have to worry about that anymore. Yes I took the easy way out for Goal #6.  

For construction and management we need some info about types for this  
![type_metadata](/images/nodesystem/type_metadata.png)  
In addition to it's destructor and different constructors this can contain anything you need to properly store the thing and fun meta-data about it like a display-friendly name, what types it can be converted to or its mothers maiden name.  

We need to have a list of all the members in a script that aren't trivially constructible/copy-able.  
There's also a thing we can do with seperating things that still need to be default constructed.  
![member_and_script_info](/images/nodesystem/member_and_script_info.png)  

Next there's the issue about the non-trivial moving.  
![script_NewFreeMove](/images/nodesystem/script_NewFreeMove.png)  

Using the prototype like design pattern, storing a "base" of a script, we can copy that data to the new allocated space and just copy/contruct the non-trivial parts at the newly allocated space.  

When it comes to entry points we just do the same thing like we did with `Flow` but apply it to the beginning of the scripts data instead of its `this` pointer.  

Conclusion  
-----------
Goal #1-4 and #6 are fulfilled, and when it comes to Goal #5  
Goal #5, Pins can only contain valid types  
is solved by the editor by only allowing connected if the TypeInfo data for the different inputs/outputs match.  

So it looks like we have it sorted out now, we still need to register some meta-data with the nodes, deal with construction of the script prototypes and there's no saving/loading explained here.  
My focus here was to keep the time it takes to run a script of nodes down, to minimize impact on performance, and add some extra help with named pins.   