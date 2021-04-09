---
layout: page
permalink: /project/2/
---
<iframe width="704" height="396" src="https://www.youtube.com/embed/GX6pTEOs6Kc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Major tasks for this project
-----------
For this project my most significant contribution is for the moving parts, everything from handling input to how they interacted with the world. I created a setup where you could define the movement limits on all parts, and sync them up by connected them to different gameobjects in unity, I think there were 4 different types of interactible elements.  
Our level designers used the tools to great effect by connected them in all kinds of fashions, I also helped a bit with connected to create prefabs such as the stairs.

I was also tasked with player movement and pathing, this proved a challenge as unitys navmesh was not sufficient to wrap around all the moving things and all the movement causing dynamic shenanigans.  
Instead I used an obstacle based approach, as everything is along a grid it was made easier by pretending it's tile-based and testing if the tiles can be reached properly.