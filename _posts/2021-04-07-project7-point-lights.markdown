---
layout: post
title:  "Tiled point lights."
date:   2021-04-07 13:10:47 +0200
categories: project7 update
---
The issues causing less than great performance:  
Level Design placed hundreds of lights in their levels.  
A problem area were the long "well"-lit corridors that occasionally popped up.  
* This caused a lot of overdraw when rendering the light meshes.
* Up to 100s of samples from the same pixel in the GBuffer.
For reference, the lighting, looking down the corridor, took 14-15ms on my PC.

I quickly realised that half of every point light (or more) were OUTSIDE the corridor.  
They were placed on walls and ceilings, so if you could use the depth buffer it would filter out almost half of all the pixels that needed lighting.  
With the inverted light meshes used that was not possible
* The mesh would always appear behind the depth.
If you flip it again so it's not inverted and render the outside
* any point light covering the player simple wouldnt get rendered.

A solution, split point lights up.
* Use the method rendering inside light mesh for lights overlapping the camera.
* Use the method rendering outside light mesh for other lights, these can use the depth buffer.

Conclusion: Decent performance boost, it's now down to 9-10ms per frame. Still bad.  

What next then, googling deferred point light rendering turned up tiled rendering, so this is how I implemented it.  
Setup code to toggle between existing rendering and the new tiled at runtime. Using Dear ImGui  

    static bool RENDER_POINT_LIGHTS_TILED = false;  
    ImGui::Begin("DebugTools");  
    if (ImGui::TreeNode("Deferred Renderer Lights"))  
    {  
        ImGui::Checkbox("Render Tiled?", &RENDER_POINT_LIGHTS_TILED);  
        ImGui::TreePop();  
    }  
    ImGui::End();  

Time to learn compute shaders then. (DirectX11)
* Loaded like other shaders, no issue.
* Takes resources like other shaders, no issue.
* Uses Dispatch instead of some Draw call, no issue.  

No issues so far, seems to be pretty straight-forward. Then I got hit by this.
* numthreads, defines number of threads per group, in x,y,z .
* SV_GroupID, based on the Dispatch(x,y,z) call from C++.
* SV_GroupThreadID, within the range of numthreads.
* SV_DispatchThreadID, this is the result of SV_GroupID * numthreads + SV_GroupThreadID.
* groupshared, values shared between all the threads in this group, not quite atomic.
* Interlocked Functions, Thread safe manipulations of groupshared values.

Tiled shading works by
1. Sending all the lights to the shader.
2. Splitting the screen into smaller parts. like 16x16px.  
3. Culling the lights by that parts frustum.
4. Rendering only the remaining lights on tile.

First we wanna make it work to know that we're doing it right.  
So lets skip part 2 and 3 for now, they're there for optimization later.

First create a new buffer to store all the lights and their colors.
We can define this in 2 `float4` variables for the shader.

    struct PointLightData {
        float4 myPositionAndRange;
        float4 myColorAndIntensity;
    };

And a matching struct for the C++ code.

    struct PointLightData
    {
        Vector4 myPositionAndRange;
        Color myColorAndIntensity;
    };

A lightbuffer with space for 256 lights were created and the framebuffer from the earlier method were appended with a `myLightCount` variable. Small change to how UV is converted to viewspace since compute shaders go per-pixel and doesn't care  about the rasterization step.

Step 1 is done, now to go for step 4, we're just gonna let it run through all lights instead, with the usual per-pixel lighting.

We just made it work! Time to make it right then!

Let's go with step 2 before 3, splitting into smaller parts of the screen is not that hard.  
Dispatch less groups increase numthreads, started with 16x16 groups on the screen.  
No problem since we still got the SV_DispatchThreadID to get the exact pixel we're working on.

Step 2 turned out to be no problem at all. Let's go to the culling then with step 3.

We need a groupshared array to contain the lights and a counter for how many filled it.  
To save space we only save the indices in the array.  

    groupshared uint ourLightIndexCounter;
    groupshared uint ourLightIndices[MAX_LIGHTS_PER_TILE];

Let each thread construct a frustum. Ignore Z for now.  
Calculating the point on the view (-1x to 1x, -1y to 1y) from the px coordinates.  
Use the projection Matrix to get the normal direction to the corners.  
Then to the view Matrix to get the plane in world coordinates.  
The plane is never getting rotated or moved, so we can define it efficiently with the [Hesse normal form](https://en.wikipedia.org/wiki/Hesse_normal_form).

Next we have to cull the point lights, we can split these up across all the threads like this.  
Not actual code, just to show you how it's splitting the work.

    uint startI = SV_GroupThreadID.x + SV_GroupThreadID.y * NUMTHREADS_X;
    for (uint i = startI; i < myPointLightCount; i += NUMTHREADS_X * NUMTHREADS_Y)

In the loop we test if the distance from the point light position to the frustums planes are all less than the radius of the point light.  
If they are we increment the counter and push in the index to the shared array.  
Then we sync all the threads in this group, before applying lighting.  

Finally instead of looping over all lights per pixel while lighting, we loop over all indices in our shared indices and pick out the correct lights.

Without culling lights on the CPU, it takes about 5ms now. Reasonable now.

It's now done "right". A few things I would like to do for improvements are.  
Completed:
* Split Position and Color in different buffers on the lights, better data locality since we're not accessing position until shading. 4ms now.
Considered or NYI:
* Near + Far plane on tile culling to prevent lights outside the depth from passing the cull.

The specs I've been running this on are
2560x1440 resolution
Intel Core i5-8600k 3.6GHz (6 CPUs)
Nvidia GeForce GTX 1060 6GB