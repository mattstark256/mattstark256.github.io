---
layout: post
title:  "Unity Minecraft Clone"
date:   2018-10-15 09:47:00 +0100
tags: Unity
---
In August 2018 I made a Minecraft clone using Unity. I primarily focused on recreating the world generation. The world generates endlessly in all directions and only loads the chunks within a fixed radius of the player. The chunks are persistent, so if you build something then walk until your building is outside the loaded area then return, it will still be there.

Chunks are made up of two types of data: blocks, which are stored as a 3D array of integers, and objects, which are stored as prefab references along with some additional data such as rotation. This means chunks can contain objects with components, for example torches that emit light.

One of the main challenges of the project was trying to get a stable framerate. For example, the process of generating a chunk can have an impact on performance, so the chunks that need to be generated get added to a queue to prevent multiple chunks being generated in the same update. Another challenge was generating objects which span multiple chunks. A tree that is generated in one chunk might overlap a chunk which has not yet been generated. I dealt with this using the same method that Minecraft uses, by generating an unloaded strip of chunks that have not yet had trees generated surrounding the chunks that have.

<iframe width="560" height="315" src="https://www.youtube.com/embed/emh_jlQ7LcE?rel=0" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>