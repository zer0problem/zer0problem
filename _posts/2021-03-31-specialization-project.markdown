---
layout: post
title:  "Specialization!"
date:   2021-03-31 12:35:47 +0100
categories: specialization update
---
My specialization is about procedural generation, I chose it because I felt I had enough back-end references and wanted to do something more visual and creative.

The goal was to make a world that would lay the foundation for level designers to work on, so that they could tailor the world to their needs instead of spending time creating the world, while also following reference material.

On other notes, I'm counting each day as about 4 hours of work as we're doing this half time, I'm not counting time I've spent collecting and presenting other things for my portfolio. I've been using godot, because I wanted to learn some of the math 

Reference image genorously provided by [Drilon](https://drilonsadiku.artstation.com/)
![Reference](/images/spec_ref_art.gif)  

Day 1  
I focused very much on getting the proper shape of the floating islands.
Creating one blob then splitting it seemed like a good choice to get the islands to seem connected, much like when the continents split during pangea. That way the borders stayed the same shape and matched like puzzle pieces.
![Day1 result](/images/spec_day1_1.gif)  

Day 2  
Started by smoothing the current shapes with more subdivisions. Later I overlayed with a net of vertices to create enough detail in the vertices to create smooth surfaces.  

It was merged by using [Delaunay triangulation](https://en.wikipedia.org/wiki/Delaunay_triangulation), a problem that arose from that was that it does not deal with concave meshes. This could be solved fast without intersection tests by checking the order of indices in the resulting triangle.  
If they matched the original range of the border mesh, they were on the edge.  
If they were on the edge, they were expected to go counterclockwise along the original range of indices. This was done by comparing the order of indicies and required no look-up on verticies or adjecent points on the original mesh.
If they were on the edge of the mesh, but the indices went in the opposite direction, they were outside the target mesh, and gently asked to leave from the result.  

I also applied a simplex noise to get the height of it, using the same simplex noise I could also get the normals. It was one merged heightmap to further display the world as one connected world and not a series of random floating islands.
![Day2 result](/images/spec_day2_1.gif)  

Day 3  
This was not the most productive day, I spent some time trying to figure out how to get blue noise generated. ([Here](https://blog.demofox.org/2017/10/20/generating-blue-noise-sample-points-with-mitchells-best-candidate-algorithm/)'s a blog post about blue noise)  
More time was spent on generating tree meshes the give out randomly to the trees.  
![Day3 result](/images/spec_day3_1.gif)  

Day 4  
Applied some simplex noise to where trees are allowed to exist, no more advanced than that.  
Reserved space for cities at the most hub-like locations, those that are on edges and closer to several other islands.  
An additional constraint on amount of cities generated were made and ensured it was done on the largest islands.  
The size of building in the cities were based on the distance from center, where the biggest city were considered the "initial" city and were given shorter building in the center to give the illusion of older buildings before it expanded.  
Also I got mad and slammed my hand in the bench, this caused all the different parts to float away from eachother. :(  
![Day4 result](/images/spec_day4_1.gif)  

Day 5  
Received comments about how few trees there were, increased tree count by many, I didn't count exacly but I decreased the distance they needed to have from other trees.  
Throwing out pillars and connections to keep the world from drifting apart. No fancy algorithms used, just tried which ones had a clear line of eachother, spawned a tower at the edge and connected them after they drifted apart.  
![Day5 result](/images/spec_day5_1.png)  
