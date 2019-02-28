---
layout: post
title: "Volumetric Clouds"
date: 2019-02-27 12:00:00 +0100
tags: Unity
---

The video below shows a Unity shader I made which renders volumetric clouds using ray marching. Ray marching is a method of rendering volumetric data where a ray is cast from each pixel and the volumetric data is sampled at evenly spaced points along the ray.

[Link to the project on Github][Github-repository]

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/8TF5hiHlf7w?rel=0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>    

The shape of the clouds is defined by a tiling perlin noise texture. This texture is used as a heightmap for the bottom and top surfaces of the clouds. The heightmap is inverted for the bottom surface. Only the volume above the bottom and below the top is rendered, as shown in the cross section below. A small amount of moving noise is also added to both surfaces to simulate turbulence.

![Clouds Diagram]({{ "/assets/volumetric clouds/CloudDiagram.png" | absolute_url }})

The ray marching samples all have the same vertical spacing, regardless of the direction of the ray, as shown below. This ensures the clouds can always be rendered using a fixed maximum number of samples. It also means that clouds closer to the viewer are rendered with higher fidelity than clouds further away. The opacity of each ray's samples is scaled according the distance between samples. Once a ray has been almost entirely blocked by cloud, it doesn't sample any further.

![Clouds Diagram 2]({{ "/assets/volumetric clouds/CloudDiagram2.png" | absolute_url }})

The colour of each sample is based on the distance from the top surface. This is to make the undersides of the clouds look like they are in shadow. The opacity of each sample is based on the distance to the nearest of the two surfaces, which helps to smooth out sharp edges caused by the ray marching.

[Github-repository]: https://github.com/mattstark256/volumetric-clouds