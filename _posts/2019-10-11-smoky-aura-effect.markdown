---
layout: post
title: "Smoky Aura Effect"
date: 2019-10-11 12:00:00 +0100
tags: Unity
---
I recently created an effect that makes an object emit rippling smoke and thought I'd try explaining my approach.

Here's a clip of the effect in action:
<iframe width="560" height="315" src="https://www.youtube.com/embed/FmKtzEKQUMA" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

The effect consists of a partially transparent texture on a billboard (a quad that rotates so it always faces the camera). It's roughly centered on the dragon and is large enough to contain the smoke.
![Billboard]({{ "/assets/smoky aura effect/Billboard.png" | absolute_url }})

First I render a copy of the camera's view containing only a silhouette of the dragon. I use a camera that has a black background and only renders one layer of the scene. You could alternatively use the stencil buffer, but accessing that after a scene has been rendered is tricky in Unity.
![Mask]({{ "/assets/smoky aura effect/Mask.png" | absolute_url }})

I then find the screen co-ordinates of the corners of the billboard and sample the corresponding region of the texture.
![Cropped Mask]({{ "/assets/smoky aura effect/Mask Clipped.png" | absolute_url }})

I blend the silhouette texture onto the existing billboard texture. This simulates smoke being emitted from the source at a constant rate and the rest of the smoke gradually fading away. I then distort the billboard's texture using velocity vectors based on a flow map. This is a texture where red and green values represent x and y components of velocity. I also add a constant upwards velocity. Progressively distorting the texture simulates currents in the air pushing it around.
![Flow Map]({{ "/assets/smoky aura effect/RG Perlin.png" | absolute_url }})
![Billboard Texture]({{ "/assets/smoky aura effect/Flow.png" | absolute_url }})

Finally I use the texture in a material applied to the billboard. The material's shader converts black and white to transparent and black. It fades out where it's close to the surface behind it so you don't see it clipping through the dragon. It also fades out near the edges of the billboard to prevent any sharp edges being visible.

And that's it! I'm really happy with how this effect looks and how low the performance impact is. It would be possible to create a simpler version for something like a flaming torch that just reads a silhouette from a texture but in that case it might be better to simply use a flipbook texture.