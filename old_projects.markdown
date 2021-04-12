---
layout: default
permalink: /old_projects/
---

Older TGA projects
===========
This is a collection of the older TGA projects I've made in different groups during my time at TGA.  
I have also included my contributions for all projects and what I've learned from them.



Project 5 - Carolina
===========
<iframe width="500" height="279" src="https://www.youtube.com/embed/QdrLHtOcbOU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Contributions to this project
-----------
During this project I set up the GameObject & Component system, Sprites and colliders to set up some base. Using what I learned in earlier projects about what worked, I made a new sprite system with support for normalmaps. I also dealt with the player items & it's usage in the game world.  
After the initial setup of base systems I worked almost exclusively on the player and its items/tools used. 

What I learned
-----------
Here I spent more time working on over-arching systems, together with other people more than I worked in earlier projects, it was a pretty fun experience and lots of programming ideas were exchanged in a good way.



Project 4 - Reapers Apprentice
===========
<iframe width="500" height="279" src="https://www.youtube.com/embed/gSVP5eEvJZk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Contributions to this project
-----------
Here we used a modified version of project 3s sprite wrapper, not a lot of setup were made in favor of rapidly getting a working player. I spent a lot of time to get the movement and collision right for the game, I put most of my work into grace periods and intervals in which the player could still perform actions to ensure the game just felt right to play and run around in. 

What I learned
-----------
Here I learned about communication and a bit about trying to reuse too much. You cannot fit a square peg into a round hole..  



Project 3 - Purgastory
===========
<iframe width="500" height="279" src="https://www.youtube.com/embed/vI2_C07b96E" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Contributions to this project
-----------
Early in the project I set up a bit of a wrapper around the sprite class in the Tga2D engine we got from the school.  
The main goals of it were to
* allow automatic batching for performance
* support tilesheets and animations
* use a pixel coordinate space with 640x360 dimensions
In my opinion it served us pretty well, the biggest thing was not having to deal with 0->1 default space in Tga2D helped a lot with communication between level designers, artists and progreammers, because now we all dealt with a 640x360 screen, when designing, rendering, animation, collision and working in for 2d art.

After the initial setup I worked on the bosses for the remainder of the project, I worked closely with the artist responsible for the sprites and in a boss taskforce to create their different attack patterns.  
The bosses are each split into separate body parts that I got the offsets for and animated in the behavior code to achieve direct feedback into their animations from their phases and attack patterns.

What I learned
-----------
This was one of my favorite projects and I obtained valuable experience in co-operation between the different roles at TGA. It was lots of work together with artists and designers to make sure everything looked and felt right to them.



Project 2 - Bellbot
===========
<iframe width="500" height="279" src="https://www.youtube.com/embed/GX6pTEOs6Kc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Contributions to this project
-----------
For this project my most significant contribution is for the moving parts, everything from handling input to how they interacted with the world. I created a setup where you could define the movement limits on all parts, and sync them up by connected them to different gameobjects in unity, I think there were 4 different types of interactible elements.  
Our level designers used the tools to great effect by connected them in all kinds of fashions, I also helped a bit with connected to create prefabs such as the stairs.

I was also tasked with player movement and pathing, this proved a challenge as unitys navmesh was not sufficient to wrap around all the moving things and all the movement causing dynamic shenanigans.  
Instead I used an obstacle based approach, as everything is along a grid it was made easier by pretending it's tile-based and testing if the tiles can be reached properly.

What I learned
-----------
Here I got great experience working tightly with level designers to make sure they had the tools they needed.  
I often found myself creating prefabs to showcase how they can create interesting connections between different gameobjects and creating new tools as needed.



Project 1 - Mars One
===========
<iframe width="500" height="279" src="https://www.youtube.com/embed/4jJNPRYcHkk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Contributions to this project
-----------
I was mainly tasked with SVN Support, as the only member that had earlier experience with version control systems, most of my early time I spent helping level designers and graphical artists with syncing everything, there was a recurring problem of the meta files from unity not syncing correctly resulting in missing meshes.  
This happened when someone added a file without importing it to unity, causing everyone elses unity to import it and assign it a GUID, that then conflicted between different workstations, as a workaround I opened level designers files and and replaced the faulty GUID with the correct ones from the SVN server.  

I was also responsible for large parts of the final player movement and feedback, working closely with artists to make sure it all looked like they envisioned.

What I learned
-----------
This was my first introduction to the agile workflow used at TGA and my first project with a lot of people, I learned a lot about communication, presenting my thoughts and opinions.