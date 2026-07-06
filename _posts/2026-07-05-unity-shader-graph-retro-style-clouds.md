---
layout: "post"
title: "Unity Shader Graph retro-style clouds"
post_url: "unity-shader-graph-retro-style-clouds"
---

I was in need of some rather simple clouds for my Unity project. I was specifically looking for clouds that would look good in a night time setting. I wanted them to be easily configurable so that I could have different skyboxes per scene without actually having to draw additional skyboxes. My game is inspired (but not limited) by early PlayStation graphics. I could have gone in multiple directions with that in mind. I went with a rather simple solution, randomly generated noise on a plane. There's many resources on the internet that lay out how to achieve a similar effect, but none that did quite what I wanted. So let this be another!

<video src="/assets/2026-07-05-unity-retro-clouds/clouds-2.mp4" muted autoplay playsinline loop></video>
<video src="/assets/2026-07-05-unity-retro-clouds/clouds-4.mp4" muted autoplay playsinline loop></video>
<video src="/assets/2026-07-05-unity-retro-clouds/clouds-8.mp4" muted autoplay playsinline loop></video>
<video src="/assets/2026-07-05-unity-retro-clouds/clouds-6.mp4" muted autoplay playsinline loop></video>

Thank you for reading <3

## The shader

For the sake of this page I will go with the assumption that you have some basic knowledge of Shader Graph, enough to re-create my set up from screenshots alone. I will go over what the shader looks like and what it does, as well as some choices I made.

The final shader will end up looking like this. Don't worry, zoomed in screenshots are below. This shader is applied to a material on a flat plane positioned in the sky.

<figure>
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-1.jpg" alt="Unity Shader Graph set up of random generated clouds" width="1672" height="1235" loading="lazy" />
  <figcaption>Final Shader Graph</figcaption>
</figure>

### Clouds

The first step is generating noise, moving that noise with time, and applying a color to the final result. Let's start off with grabbing the position for where the clouds will appear. This uses a UV position node. In this case I am using a custom sub graph named "Pixelate World Position UV". I will get into pixelation later, but for now you could replace this subgraph with a <code>Position</code> node set to <code>World</code>, the rest of the graph will look the same. This node is split into two components, the X and Z axis only, which are fed back into a Vector 2 node. We only need the X and Z components since the clouds will only move horizontally, there is no vertical movement.

To move the clouds we add a <code>Time</code> node and multiple the <code>Time</code> value with a variable that determines the speed, simply named "Clouds speed". We add the result of this and the previous <code>Vector 2</code> node together to get our position. This will be fed into several other nodes requiring a UV.

<figure>
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-4.jpg" alt="Unity Shader Graph showing a subset of nodes for position of UV position based on time" width="1062" height="571" loading="lazy" />
  <figcaption>Nodes for the UV position of the clouds</figcaption>
</figure>

With the UV set up we generate two noise patterns. For both we use <code>Simple Noise</code> as this creates the most random noise patterns without islands. One of the <code>Simple Noise</code> nodes will use "Clouds scale" as its scale, the other "Clouds noise scale". The first variable determines the overall size and shade of the clouds, the white bits, the second determines smaller details and shadows within the cloud. "Clouds scale" will be set to a small value around 0.05-0.1. A smaller value will mean you get larger clouds, a larger value will result in a much noisier appearance which might be neat when trying to create rain or storm clouds. "Clouds noise scale" is set to something about 10 times higher, along the lines of 0.2-1, creating more detail within the cloud. Both of these are added together and finally multiplier by a "Clouds color" value. This is used to color the final result, as you might have inferred. This is useful to create darker clouds or different colored clouds during different periods of the day.

The final result of the multiply is plugged into the "Base Color" of the fragment section, and that's our colors done!

<figure>
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-5.jpg" alt="Unity Shader Graph showing a subset of nodes for noise generation and coloring" width="1172" height="893" loading="lazy" />
  <figcaption>Nodes showing noise generation and coloring</figcaption>
</figure>

### Alpha

What we have so is a constant layer of clouds across our sky with nothing of the clouds showing through. First up we'll create a new noise node, this time using <code>Gradient Noise</code> as this allows us to nicely create individual clouds, rather than one continuous sky. The <code>Gradient Noise</code> is fed the UV we created early as well as an "Alpha scale" value that will determine the size of the individual clouds. This is fed into a <code>Smoothstep</code> node. This node allows us to essentially adjust the contrast and gradient of each cloud. For instance setting a value of x=0.1 and y=0.2 will create very clear gaps in a fill sky, x=0.7 and y=0.8 will do the opposite, create very defined clouds. Larger deltas will create much smoother transitions. x=0 and y=5 will create very soft transitions. Play around with these values to see what works best for you.

This smoothstep is multiplied by the "Clouds scale" simple noise from earlier. This is optional but helps breaks up patterns and helps make the alpha look more in line with the clouds themselves.

The edge of the plane will show a clear cut-off line from which the clouds appear (more on this later!). The first step to alleviating this issue is adding a fade along the edge. For this we add another UV to get the <code>Distance</code> of each pixel to the center. We feed this into another <code>Smoothstep</code>, here the "Alpha edge smooth step" value is used to determine the harshness of the transition. Once again, see what values work well for you. This is fed into a <code>One Minus</code> node, which simply inverts the previous node.

The output of the Alpha and Edge Fade blocks are multiplied together and fed into the "Alpha" of the fragment section, and with that we have well defined clouds!

<figure>
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-6.jpg" alt="Unity Shader Graph showing a subset of nodes alpha based on noise and alpha used as a fade along the edges of a UV" width="1230" height="1131" loading="lazy" />
  <figcaption>Setting the alpha to both create islands of individual clouds and fade the edges of our plane</figcaption>
</figure>

### Hiding the edge

We apply our material to a flat plane high up in the sky, but when looking far into the distance the end of the plane is quite clearly visible. This is alleviated somewhat by the edge fade we set up, but that ends up with sky in the distance having no clouds at all.

<figure>
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-7.jpg" alt="Unity scene showing clouds hitting the edge of a plane, creating a sharp cut-off" width="1512" height="829" loading="lazy" />
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-8.jpg" alt="Unity scene showing clouds with wireframes on a flat plane" width="1524" height="831" loading="lazy" />
  <figcaption>Sky in the distance has no visible clouds</figcaption>
</figure>

We could make our plane much larger, but if we have a rendering distance set that won't help much. Instead we will be changing the position of the vertices to create a dome-like shape.

First up we get create a <code>Position</code> node set to Object. Our plane has no height, so all we want are the X and Z value, which we get from the <code>Split</code> node. We feed both of these into a <code>Vector 2</code> node to combine them and feed this into a <code>Length</code>, getting the magnitude of our vector. This is fed into a <code>Power</code> node, which will displace each vertex by their position. To control by how much we multiply this by a "Curve multiplier" value. This will by a small value around 0.05 and 0.1, depending on how large and high up your plane is. This output is the final Y value of each vertex and is fed into a <code>Combine</code> node, combined with the same X (R) and Z (B) values from our <code>Split</code> from earlier, getting the final XYZ value of each vertex. This then is fed into the "Position" of the vertex section.

<figure>
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-2.jpg" alt="Unity Shader Graph showing a subset of nodes for dome vertex displacement" width="1613" height="961" loading="lazy" />
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-9.jpg" alt="Unity scene showing clouds with wireframes on a dome shape" width="1529" height="832" loading="lazy" />
  <figcaption>Using vertex displacement to create a dome-like shape, wrapping the plane</figcaption>
</figure>

## Pixelation

For my particular use case I wanted the clouds to be showing clearly defined pixels as that fits the style of my game. This might not apply to your game, in which case you can simply use a <code>Position</code> node set to "World" as mentioned earlier, in which case, you're done!

<figure>
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-13.jpg" alt="Unity scene showing clouds above a copse of trees" width="1554" height="836" loading="lazy" />
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-12.jpg" alt="Unity scene showing pixelated clouds above a copse of trees" width="1548" height="831" loading="lazy" />
  <figcaption>The difference between pixelated and non pixelated clouds, the amount of pixelation can be controlled with a variable</figcaption>
</figure>

Pixelation is something I use throughout more graphs and I've opted to use a Subgraph to avoid repetion. This has a single variable "Pixel size", where a lower value means larger individual pixels.

We grab a <code>Position</code> node set to "World". This is multiplied by the "Pixel size" value. From here we subtract the value with 0.5, centering the UV. This value is rounded and divided by the same "Pixel size" value, rounding each UV coordinate to the nearest pixel value. This subgraph is than used instead of any World Position node.

<figure>
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-3.jpg" alt="Unity Shader Graph showing a subgraph to pixelate a world position UV node" width="1992" height="842" loading="lazy" />
  <figcaption>Subgraph to pixelate a World Position UV node</figcaption>
</figure>

## Final material

And there we go, our final material. Here are two values I used through this article, one for day time scenes and the other for night time scenes. As this is an unlit shader, the night time variant uses a much lower alpha  (determined by the "Alpha smooth step") value to make the clouds less bright.

From here you can do whatever you want. One neat trick to create a bit more depth is to add a second plane with a slightly different material. Perhaps one with faster moving clouds, less defined details, or anything else.

<center>
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-10.jpg" alt="Unity material settings showing for clouds for a day time scene" width="420" height="377" inline loading="lazy" />
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-11.jpg" alt="Unity material settings showing for clouds for a night time scene" width="424" height="373" inline loading="lazy" />
</center>

<figure>
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-15.jpg" alt="Night time scene with soft clouds" width="1535" height="823" loading="lazy" />
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-14.jpg" alt="Night time scene with heavy clouds" width="1527" height="823" loading="lazy" />
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-16.jpg" alt="Day time scene with dense soft clouds" width="1535" height="833" loading="lazy" />
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-17.jpg" alt="Day time scene with sparse clouds" width="1525" height="829" loading="lazy" />
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-18.jpg" alt="Day time scene with a cloud covered sky" width="1520" height="831" loading="lazy" />
  <img src="/assets/2026-07-05-unity-retro-clouds/clouds-shader-19.jpg" alt="Evening scene with soft orange clouds" width="1517" height="827" loading="lazy" />
  <figcaption>Several different end results</figcaption>
</figure>

Thank you for reading <3
