---
layout: page
title: RiktigMotor
permalink: /riktig_motor/
---

RiktigMotor
===========
I did not think this name would stick.  

There will be markings such as P6, P7 och P8 depending on what project that added it.

Supported features (sorted by how cool I think they are)
* (P7) tiled deferred shading with point lights [explanation](/project7/update/2021/04/07/project7-point-lights.html)
* (P7) forward+ rendering
* (P7) spot lights with cookies & shadows <a class="link" href="/images/project7_spotlight_shadow_and_cookie.gif">example<img class="preview" src="/images/project7_spotlight_shadow_and_cookie.gif"></a>
* (P7) Node scripting system [backend explanation](/project7/update/2021/04/11/node-system.html)
* TODO more here

These third party libraries and technologies are used
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