---
layout: default
title: RiktigMotor
permalink: /riktig_motor/
---

RiktigMotor
===========

Supported features
-----------
Since [Project 7](/project/7/)
* Tiled deferred shading with point lights [explanation](/project7/update/2021/04/07/project7-point-lights.html)
* Forward+ rendering
* Spot lights with cookies & shadows <a class="link" href="/images/project7_spotlight_shadow_and_cookie.gif">example<img class="preview" src="/images/project7_spotlight_shadow_and_cookie.gif"></a>
* Node scripting system [backend explanation](/project7/update/2021/04/11/node-system.html)
* Frustum Culling, made by [Andreas](https://www.andreasrunsjo.com/cullingsystem)
* 3D Audio
* Physics using physx
* "Ordered" transparency  

Since [Project 6](/project/6/)
* Deferred and Forward Rendering 
* Particle Systems
* PBR
* Skeletal animations
* Directional Shadows

Other notes
-----------
Render order
* Culling by [Andreas](https://www.andreasrunsjo.com/cullingsystem), includes spotlight shadows
* Deferred rendering for majority of models
* Render opaque models to spotlights shadowmap
* Tiled shading & culling point lights for forward+
* Spotlight shading (not yet tiled, it's on my list)
* Sort for transparent objects
* Sort includes particles
* Particles are unlit (NYI lit particles)
* Lets particles be sorted properly with windows etc
* Forward+ pass for transparents & particles(Unlit)
* Post-processing
* Immediate Sprites & Text rendering
* ImGui & other debug overlays
* Present!

Current focus
-----------
One of the things I wanna focus on is transparent objects, I've had some fights with getting it right for our artists, I've been looking with one eye at different order independent transparency solutions and other approximations for a better result.  
Another thing I want is faster spotlights and shadows, perhaps look closer at clustered or hybrid binning of lights and doing less in the deferred stage of rendering, it is rather bandwidth consuming, and I think an improved forward renderer could outshine our current default-to-deferred render order.  
After all, the performance can never be too good and the quality is always lower than you want, constant pushing is required!  

These third party libraries and technologies are used
-----------
There will be markings such as P6, P7 och P8 depending on what project that added it.
* (P6) DirectX11 for rendering
* (P6) [entt](https://github.com/skypjack/entt) as an entity component system 
* (P6) [assimp](http://assimp.org/) for model loading
* (P6) [DirectXTK](https://github.com/Microsoft/DirectXTK) for .dds file loading and simplemath
* (P6) [miniz-cpp](https://github.com/tfussell/miniz-cpp) for zipping crash logs for easy error reporting
* (P6) [SoLoud](https://sol.gfxile.net/soloud/) for audio/sound
* (P6) [NLohmanns json for modern C++](https://github.com/nlohmann/json) for json parsing/saving (simdjson can only read files, so still kept around)
* (P6) [Dear ImGui](https://github.com/ocornut/imgui) for debugging tools
* (P6) [ImGuizmo](https://github.com/CedricGuillemet/ImGuizmo) for tools and debugging purposes
* (P6 dropped in P7 in favor ov imgui-node-editor) [imnodes](https://github.com/Nelarius/imnodes)
* (P7) physx for physics (since project 7)
* (P7) [simdjson](https://simdjson.org/) for quickly loading json files
* (P7) [imgui-node-editor](https://github.com/thedmd/imgui-node-editor) for tools