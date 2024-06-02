---
layout: post
title: Portal light propagation part 1
date:   2024-06-02 17:10:00 +0200
categories: portals update
author: Tommi
---

Summary  
-----------
An implementation of portals that correctly propagate light, removing as many visible seams and artifacts as I possibly can. Performance is good if possible, but getting it working is the main priority here.

These were my goals  
-----------
To provide the simplest solution I could find for these problems, explain why the problems exist and how I've solved them. There are methods that will give better performance and quality than the methods presented here but they will generally detract from the problem itself by increased complexity and has been omitted for now. It is my intention to write follow-ups on each individual part in greater detail with more optimized methods of dealing with the issues.

What engine to use?
-----------
The main factor is that I realized I'd have to modify the engine and I don't have access to the source code of Unity and my educated guess is that the rendering source of Unreal is more complicated than Godot.

The second reason is iteration time. Compiling Godot is fast and reloading scripts is pretty much instant, even while testing.

Portals, the naive solution  
-----------
This is a solution I've seen a few times around and it starts with a viewport, camera, and a plane for a single portal.
The basic method is that you move the extra camera from the where the portals input is to where the output is. The camera renders to the viewport. When the plane is rendered it samples from the viewports texture.

It can look something like this.
![simple_viewport_portal](/images/portals/simple_viewport_portal.png)  

This naive method uses a viewport for each portal. This can be optimized by sharing space on a viewport and using a viewport pool instead of one per portal if you have many portals. Another optimization would be to do something else that does not use extra viewports. That's is foreshadowing for later.

But for our goals, this is seamless enough for now, we can make a more optimized version later, for now let's continue.

Portal recursion, the naive solution.
-----------
With the viewport solution above the rendering of the portal recursively is not a big challenge, the challenge is making sure it's done to separate viewports. A solution to this issue is simply to make more portals for each portal you look through and make sure that they are only visible when rendering the original camera.

Using a viewport pool rather than a viewport per portal is really helpful so you don't need to create a new viewport every time you need a new portal.

Initially I solved this using some layer mask tricks. Godot uses 32 bit unsigned integers for their layers so you have 32 bits you can use for this. First assigning a unique layer per camera that wants to render and the portals it should render on that unique layer will ensure only that camera renders those portals.

Lights, the naive solution.  
-----------
Light is needed on the other side so we duplicate all the lights affecting a portal and have them moved to its exit like the camera.
This is what it looks like
![with_lights](/images/portals/with_lights.png)  

But we immediately have some unwanted results from this. Like a seam that reminds us that this is a portal and not a continuation of the game world.

There's light coming leaking around the outside of the portal as well.
Since I'm just going for the easiest solution for now. I simply decided to store the shape of the portal and when rendering the light, check if the position the light reaches is going through the portal and if it does not, filter it away.

Lights, the issues begin.  
-----------
There's no built-in way to get what light we're rendering and as such, no way to get what portals we're looking into or out of.

The thing we need is some information about the portal associated with the light we're applying in the light() function.
First we need to know what light it is being applied and then we need to have the position local to that light so we can check the data relevant to it.

To get the identifier for the light we previously stored we have to modify the engine and add a new usage define and rename for shader compiler. This was done by adding a new built-in for the light() shader by adding it to the DefaultIdentifierActions inside SceneShaderForwardClustered init.
Then adding it to the spatial shader modes light() function as a built in done in the ShaderTypes class.

We can find that the mask is already included as a uint in light_data_inc.glsl and exposing this lets us have a unique identifier for this light.
So modifying the GLSL code in scene_forward_clustered.glsl and scene_forward_lights_inc.glsl to forward the lights mask to the light_compute function where the light() function is inserted. The unique identifier for the camera can either be obtained the same way or by using a varying.

Finally we need to get the position in the light() part of the shaders, we almost have the correct data in the VIEW and LIGHT built-ins, but they're both normalized. Forwarding the VERTEX variable from the vertex() part using a varying works fine as you can transform the position by the light and view matrix to get a light relative vertex.

Lights, the relation to portals.  
-----------
Since the current concern is to get it working, regardless of performance, I decided to make sure the data needed was available in the shaders and decide what to do after that.

The way I solved this was to put up a series of culling planes for each light. For this you could implement user clip planes, but to keep it simple and the number of engine modifications down I instead resolved this in the shader.

Each portal is defined for each light as a series of 5 planes forming a sort of frustum with a skewed near plane.
The shape looks like this, extended further out.
![this_shape_but_more](/images/portals/this_shape_but_more.png)  

Next I defined portals relation to eachother as siblings if they're the same depth and seen through the same portal, a child to any portal they're viewed through. Using this parent-sibling-child relationship meant I could iterate through all portals like a tree-graph to get the ones I need when resolving their planes.

Now comparing the light relative vertex with the portal data associated with that light we can decide if the light intersects the portal and what this does with it.

If the pixel is outside its own portal or inside any of its child portals, it's discarded. Otherwise it is kept and going to be rendered. Portal overlap was resolved by prioritizing the portal closest to the source after being sorted by depth.

The final result is pretty much worth the trouble.
![filtered_lights](/images/portals/filtered_lights.png)  

Here we can see the portals giving the illusion that it's both blocking the light and allowing it through on the other side. When in reality it's two lights helping to give this illusion.

Shadows, the naive solution.
-----------
There's still some issues with our current approach, such as with shadows, here is an example of what it looks like if we just turn on shadows and give it something that can cast a shadow.
![shadow_issue](/images/portals/shadow_issue.png)

To modify the shadows we first need to understand the basic concept of how shadowmaps work. A shadowmap is a depth buffer used to get how far the light reaches before it hits something. Learn OpenGL has a way better guide [here](https://learnopengl.com/Advanced-Lighting/Shadows/Shadow-Mapping) than I could write about it.

The problem with this is that the second lights shadow does not account for the cube. This can be solved by either merging the lights shadowmaps after they have been rendered or letting the lights render to the same shadowmap. I went for the latter method to share a shadowmap as it sounded like a simpler plan. It should also use way less shadowmaps than merging them.

Since shadowmaps are allocated in an atlas and a region is given to the light as it's needed as well as clearing it when it starts rendering, it's not as simple as just saying they use the same resource.

First thinking about the new relation between lights, I decided to add a new variable to let a light use another light as their source of shadow relevant data.
Then modifying it so the first time a light wants to render a shadow in a frame, it will clear & allocate a region of the shadow atlas, with subsequent uses only recognizing the same region.

This is what the result looks like.
![so_close](/images/portals/so_close.png)

These changes had to be done for LightStorage, Light3D and RenderForwardClustered. There's also a big issue with each light being rendered once per viewport which can quickly get expensive, this can be resolved by not doing that, by only rendering the shadows once before.  

Final touch with shadow propagation.  
-----------
We're pretty close, it's just that shadows does not filter correctly. But we don't have to over complicate this as we can just solve it by using the light filter from earlier when rendering the shadowmap.
![seamless](/images/portals/seamless.png)

That's it for now, visually seamless portals, of course there's some more things you can do like apply a PCF filter to the propagated light/shadow edge for a smoother look. Performance can always be improved by using more efficient techniques, but as an MVP, this is very seamless even if it's inefficient.

Conclusion & thoughts  
-----------
I don't think I'm done with this project, but I wanted to put something out there about this, just so I have something out there. I have not written about all the things I've done with this yet, but I omitted them to keep this as simple as possible. This may already be a lot to take in for someone unfamiliar with graphics programming.

Current progress
-----------
Some things I have already optimized are the removal of extra viewports in favor of just rendering directly to the main viewport to save space and avoid sampling from another viewport.

Then there's things that I would like to explore a bit more but requires larger modifications to the engine like user clip planes and stencil buffers so they could be used to optimize the portals and propagated lights/shadows.

Other things like CSG subtractions for collisions and affecting space & movement are things I've played around with as well, but as of right now I don't have any plans on writing about anything other than the rendering of the portals.

If you have feedback or comments about this post, you can reach me on twitter [@IsraelssonTommi](http://twitter.com/IsraelssonTommi)
