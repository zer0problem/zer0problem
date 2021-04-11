---
layout: post
title:  "Specialization finale!"
date:   2021-04-09 13:52:22 +0200
categories: specialization update
---

Finally decided to add some ground to it.  
Got the surrounding border and setup a wall for the bottom mesh.  
![Surround](/images/1_LetsBuildAWall.png)  

Moving it down so it's not covering the edges, it's just gonna be folded into the bottom side to make it a solid mesh.  
![Move it down](/images/2_LetsNotBuildAWall.png)  

The final result!  
![Final](/images/3_FoldBottom.png)  
And some wireframes to display the generated meshes.  
![Wire1](/images/SpecFinal_Wire1.png)  
![Wire2](/images/SpecFinal_Wire2.png)  

Some techniques and other notes I felt like sharing.  

All the meshed were generated, nothing was premade, several buildings and trees were generated, and the one used was randomized.  
This is easier to spot in trees than buildings, as more buildings were needed to get a decent variation with height differences.  

For the ground I used delanouy triangulation, the "outside" triangles generated outside the base border, could be identified with their index, without needing to test any positions.  
This was easily resolved as the border indicies were the first X indicies, where X is the amount of border points.  
Using that I can count how many indicies are used, if there are less than 3, then it's inside.  
If there's exactly 3 on the edge you can test their order, which is trivial from their index.  

As everything was auto-generated, I've been so far showing it off with a single seed to show clear progress, so here's a few with different seeds.
![DifferentSeeds](/images/SpecGeneration5.gif)

Final thoughts.  
In total I've spent 7 half-time days on this project or about 30 hours on this project, there's been quite a bit of trial and error.  
Most of the later time in year 2 of TGA have been spent on backend functionality and support for [RiktigMotor](/riktig_motor) used in [project 6](/project/6/) and [project 7](/project/7/) so it has been a really nice change of pace to do something where I could flex a bit more of my creative muscles, instead of the slightly more straightforward, need feature -> implement feature cycle I've been in for a while now.  
I'm not happy with the amount of time I got to spend on it, I really wished I had some more time on it to really polish it, but at the same time I realise, doing all of this manually is still a rather slow process compared to using a tool like houdini.  
I'm happy that I got to do this project and that it turned out as well as it did.