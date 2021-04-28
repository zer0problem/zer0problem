---
layout: post
title:  "Realtime performance profiling"
date:   2021-04-19 10:06:21 +0200
categories: project7 update
author: Tommi
---

Summary  
-----------
So during the development of our [project 7](/project/7/) for TGA we encountered an issue, Performance.  
Just randomly picking out things to optimize is guaranteed to result in sub-optimal usage of our time, we needed to know where, or atleast close in where the bottlenecks could be. 

These were my goals  
-----------
Find out why the performance is less than acceptable.  
Simplify the effort needed for this in the future.  

The solution
-----------
A simple stopwatch or timekeeper to sort through all the data, simply applying a start/stop at every system or part of a larger class.  
The interface would look something like this.  
![CPU_realtime_profiler_interface](/images/CPU_realtime_profiler_interface.png)  
With a little bit of usage like this.  
    RM::TimeKeeper::Start("Waiting for GPU");  
    myGraphicsEngine->EndFrame();  
    RM::TimeKeeper::Stop();  

With a StartFrame and EndFrame at the start/end of a frame, the StartFrame and EndFrame are there to sync across thread_local variables for multi-threading support... It's a real shame I have not had the time to multi-thread the main-loop of [RiktigMotor](/riktig_motor/).  

Calling display shows this ImGui window, with the following statistics for the first level in our game. These results are part of why I have not yet multi-threaded the engine, if you're still Waiting for the GPU, the frame-rate is not going to increase, regardless of how many threads you can split the work on, you're still going to wait for the GPU.  
![CPU_realtime_profiler](/images/CPU_realtime_profiler.png)  

We already have some on/offs for all(almost) major parts of the engine, using that it did not need a lot of effort to test what system used all the GPU. Spoiler, it was mostly the [point lights](/project7/update/2021/04/07/project7-point-lights.html). But that's fixed now. Finally here's an image of our DebugTools to show how we turn systems on/off in real-time.  
![DebugTools](/images/DebugTools_on_off.png)

Conclusion  
-----------
It's as important to know where to improve as it is to know how. Take your time to figure out where the bottlenecks are, a little research can save you hours of trial and error. Here's a link to the source code, beware it's not been cleaned, I just took it straight from the codebase after reading it through to see I don't give out anyone elses code.  

[TimerKeeper on github](https://github.com/zer0problem/TimeKeeper)
