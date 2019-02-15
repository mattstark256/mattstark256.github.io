---
layout: post
title: "Window Shaders"
date: 2019-02-15 12:00:00 +0100
tags: Unity
---
Some games use shaders for windows to make it look as though there is a three-dimensional interior behind the window without requiring any additional geometry. This can reduce the performance impact of rendering many interiors, for example in city scenes where there are hundreds of windows.

I created some window shaders in Unity using several different techniques to create the appearance of depth.

The most basic approach was to sample a cubemap using the view direction. This would only be effective for very large interiors.

![Cubemap Interior]({{ "/assets/window shaders/WindowCubemap.gif" | absolute_url }})

A more convincing effect can be created by having an interior texture that is sampled as though it were positioned some distance behind the window's surface. This can be done efficiently, because most of the calculations can be in the vertex shader.

![Plane Interior]({{ "/assets/window shaders/WindowPlane.gif" | absolute_url }})

(code)

I also wanted to replicate was the window shader used in Forza Horizon 4, as discussed in [this Gamasutra article](https://www.gamasutra.com/view/news/332409/Game_Tech_Deep_Dive_A_window_into_Playground_Games_latest_shader_development.php). This shader takes a square 2D texture and projects it so it appears to be a cuboid-shaped interior.

The gif below shows my shader in action, as well as the 2D texture. I've used a bay window to demonstrate that it works regardless of the shape of the window's mesh.

![Forza Interior]({{ "/assets/window shaders/WindowForza.gif" | absolute_url }})
![Interior Texture]({{ "/assets/window shaders/Room Thirds.png" | absolute_url }})