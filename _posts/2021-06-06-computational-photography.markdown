---
layout: post
title:  "Computational Photography"
date:   2021-06-06 20:30:00 +0800
---

As promised, I come to share some results from the course projects of UIUC OMCS CS445 Computational
Photography.

First, a quick introduction of Computational Photography:
"Computational photography is an emerging field created by the convergence of computer 
graphics, computer vision, and photography. Its role is to overcome the limitations of the 
traditional camera by using computational techniques to capture, enhance, and combine imagery 
for a more vivid and lifelike visual experience."[1]
More about the subject and CS445 can be found on the
[course page](https://courses.engr.illinois.edu/cs445/sp2021/).

The course has 5 projects and 1 final project.
I only did the first 4 projects out of the 5, which is enough for me to get all the points needed,
thanks to how grading of the course works.
Now without further ado, here are the projects:

### Table of Contents <a name="toc"></a>
* [Project 1: Hybrid Images](#project-1:-hybrid-images)
* [Project 2: Image Quilting](#project-2:-image-quilting)
* [Project 3: Gradient Domain Fusion](#project-3:-gradient-domain-fusion)
* [Project 4: Image Based Lighting](#project-4:-image-based-lighting)
* [Final Project: GrabCut](#final-project:-grabcut)

### Project 1: Hybrid Images <a name="project-1:-hybrid-images"></a>

#### Hybrid Images

Most of the time, we see an image in the form of an intensity graph, with each pixel containing
information of intensity of each of the red, green, and blue channel.
However, images can also be represented in the frequency domain without any loss of information.
Viewing images from the frequency domain can offer us new insight
(e.g. the frequency distribution of images of natural scenes and that of images of artificial 
objects such as architecture display distinct patterns),
and manipulating images in the frequency domain can give us interesting results.

![xrayed hand](/assets/images/2021-06-06/hand_hybrid.png)

<p style="text-align:center;">
(The skeleton layer of the image is based on 
<a href="https://commons.wikimedia.org/w/index.php?curid=63719647">work</a>
by Mikael Häggström.)
</p>

You will see different things from the above image depending on your viewing distance.
(You can also just zoom in and out the web page.)
The reason is that people are most sensitive to a specific range of frequencies at a given
distance of viewing.
When we are viewing from a close distance, we are more sensitive to the high-frequency information
which usually corresponds to texture and fine details.
When we move away from the image, the low-frequency information starts to dominate our perception.
By filtering the skeleton layer of the image to only retain the high frequencies,
it will become less obvious when viewed from afar.

#### Contrast Enhancement

Contrast enhancement by histogram equalization.

<div style="text-align:center">
  <div style="display:inline-block;">
    <img src="/assets/images/2021-06-06/contrast_to_enhance.png"/>
  </div>
  <div style="display:inline-block;">
    <img src="/assets/images/2021-06-06/contrast_enhanced.png"/>
  </div>
</div>

#### Color Enhancement

Manipulating saturaion in the HSV color space.

<div style="text-align:center">
  <div style="display:inline-block;">
    <img src="/assets/images/2021-06-06/color_to_enhance.png"/>
  </div>
  <div style="display:inline-block;">
    <img src="/assets/images/2021-06-06/color_enhanced.png"/>
  </div>
</div>

#### Color Shift

Making the image look more red/less yellow without changing the luminance/intensity by manipulating
color (applying gamma/exponentiation on the respective channels) in the LAB color space.

<div style="text-align:center">
  <div style="display:inline-block;">
    <img src="/assets/images/2021-06-06/color_shift.png" width=250/>
  </div>
  <div style="display:inline-block;">
    <img src="/assets/images/2021-06-06/color_shifted_more_red.png" width=250/>
  </div>
  <div style="display:inline-block;">
    <img src="/assets/images/2021-06-06/color_shifted_less_yellow.png" width=250/>
  </div>
</div>

[Back to ToC](#toc)

### Project 2: Image Quilting <a name="project-2:-image-quilting"></a>

In this project, I implemented image quilting algorithm for texture synthesis and texture transfer.

The goal of texture synthesis is to generate a larger image of some texture from a smaller image of
that texture.
The goal of texture transfer is to transfer some texture onto a target image.
For example, transfer the texture of sand to an image of the face of a person, so that it looks
like the face of the person is drawn in the sand.

The techniques are actually quite similar.
You have a larger image to fill in and a texture image.
You fill in the larger image line by line, patch by patch with small patches of texture sampled
from the texture image.
Each time you fill in a patch, you try to find a sample that minimizes some cost function,
which is similar but different for texture synthesis and texture transfer.

To make sure the result image looks natural, the patches you fill should have overlapping sections.
When you fill in a patch, you will also calculate a seam that cuts through the overlapping section.
The seam should minimize the total intensity difference between the pixels that are on the two
sides of the seam, the intuition being you want to cut through the overlapping section where
the difference between the two patches are the smallest, so that the cut is less obvious.

#### Texture Synthesis

The cost function for texture synthesis is simple:
sum of squared differences (SSD) of the overlapping section.

<img src="/assets/images/2021-06-06/texture_synth_sample.png"/>
<img src="/assets/images/2021-06-06/texture_synth_output.png" width=500/>

#### Texture Transfer

The cost function in this case consists of two terms:
one is still the SSD of the overlapping section
(as you still want the neighboring patches to look like they belong with each other);
the other is the SSD of the selected sample with target image.
You can use alpha times one of the terms to control the relative weights of the two terms.

<div style="text-align:center">
  <div style="display:inline-block;">
    <img src="/assets/images/2021-06-06/texture_transfer_texture.png"/>
  </div>
  <div style="display:inline-block;">
    <img src="/assets/images/2021-06-06/texture_transfer_target.png"/>
  </div>
</div>
<img src="/assets/images/2021-06-06/texture_transfer_output.png" width=500/>

<p style="text-align:center;">
(
<a href="https://commons.wikimedia.org/w/index.php?curid=40055508">Rice texture</a> by Mx. Granger.
<a href="https://knowyourmeme.com/memes/doge">Doge</a> from KnowYourMeme, 
<a href="https://kabosu112.exblog.jp/9944144/">originally</a> by Atsuko Sato.
)
</p>

[Back to ToC](#toc)

### Project 3: Gradient Domain Fusion <a name="project-3:-gradient-domain-fusion"></a>

The third project is about gradient domain processing.
The goal of the first two parts is to blend an object from a source image into a target image
and try to make the result image look natural.
The thrid part is about preserving the "color gradient" when converting a color image into
grayscale.

#### Poisson Blending

The intuition of this technique is to keep the gradient of the object from the source image
while ignoring the overall intensity.
In many cases, this will yield very good results because we usually care more about the gradient
than the intensity (color).
A hat is a hat, be it brown or black.

<img src="/assets/images/2021-06-06/poisson_copy_paste.png"/>
<img src="/assets/images/2021-06-06/poisson_result.png" width=500/>

<p style="text-align:center;">
(
<a href="https://commons.wikimedia.org/wiki/File:The_Red_moon.jpg">Picture of the moon</a>
by Giudymariapia.
)
</p>

But there are also times when color does matter. Poisson blending doesn't always work.

<img src="/assets/images/2021-06-06/poisson_failed.png" width=600/>

<p style="text-align:center;">
(
<a href="https://commons.wikimedia.org/wiki/File:Libellulidae_Brachythemis_contaminata_on_Nelumbo_nucifera_leaf_with_water_drops%2C_in_a_pond.jpg">
Picture of a lotus leaf
</a> by Basile Morin.
)
</p>

#### Mixed Gradient

The part of the image where the gradient is stronger usually contains information that are more 
interesting to us, so instead of always perserving the gradient of the object from the source
image, we could preserve the stronger gradient of either the source image or the target image for 
each neighborhood.

<img src="/assets/images/2021-06-06/mixed_copy_paste.png"/>
<img src="/assets/images/2021-06-06/mixed_result.png" width=600/>

<p style="text-align:center;">
(
<a href="https://commons.wikimedia.org/wiki/File:Surfaces_vertical_brick_wall_closeup_view.JPG">
Picture of a brick wall
</a>
by Tomwsulcer.
)
</p>

Native poisson blending would not preserve the brick texture in the area around the text.

#### Color2Gray

Color2Gray usually only retains information about the intensity but not the color difference, 
so if we convert an image used for color blind screening to grayscale,
the number in the image will become invisible.

To solve this, we could do some gradient domain processing and encode information about the
color difference in some "color gradient".
In this case, I used  the highest intensity difference (one that has the highest absolute value)
of the three color channels to compute the gradient.

<div style="text-align:center">
  <div style="display:inline-block;">
    <img src="/assets/images/2021-06-06/colorBlind8.png"/>
  </div>
  <div style="display:inline-block;">
    <img src="/assets/images/2021-06-06/colorBlind8_gray.png"/>
  </div>
</div>
<img src="/assets/images/2021-06-06/colorBlind8_result.png"/>

[Back to ToC](#toc)

### Project 4: Image Based Lighting <a name="project-4:-image-based-lighting"></a>

The goal of this project is to insert some 3D objects into a real world scene and
render the image to make everything look natural.
We can easily tell something is off if the inserted 3D objects do not have the right shadow
or the right reflections,
so to make things look natural, we will also need to obtain lighting information of the scene.

#### Data Collection and Recovering HDR Radiance Map from SDR Images

To collect lighting information, we use a mirror ball and take photos of it.
A mirror ball can actually reflect light from almost all angles to the camera.
We only lose lighting information about the part of the scene that is directly occluded by
the ball.

Another problem of getting lighting information from photos is that in SDR photos,
the brightest part cannot be more than 255 times brighter than the darkest part,
but in the real world, the brightest part can be thousands of times brighter than the darker parts.
When you take a photo, the camera has to make a choice and clip some bright spots and dark spots.
To recover the real-world radiance from one SDR photo is guess work,
but we can take multiple SDR photo of the same scene using same settings but different
exposure and calculate the HDR radiance map from these SDR photos.

<img src="/assets/images/2021-06-06/sdr_photos.png"/>
<img src="/assets/images/2021-06-06/recovered_hdr.png"/>

<p style="text-align:center;">
(SDR images and the recovered HDR image tone-mapped to SDR)
</p>

#### Panoramic Transformation

Next, we do some transformation to get an equirectangular environment map from the mirror ball 
that can be used in rendering software.

<img src="/assets/images/2021-06-06/equirect.png" width=600/>

#### Rendering

<img src="/assets/images/2021-06-06/rendered_image.png" width=600/>

<p style="text-align:center;">
(
<a href="https://free3d.com/3d-model/tea-cup-947081.html">Tea Cup 3D Model</a>
by sidhartho.
<a href="https://free3d.com/3d-model/knight-13720.html">Knight 3D Model</a>
by abitor.
<a href="https://free3d.com/3d-model/lego-man-8986.html">Lego Man 3D Model</a>
by dualityy.
)
</p>

[Back to ToC](#toc)

### Final Project: GrabCut <a name="final-project:-grabcut"></a>

The final project I did was implementing GrabCut described in this
[paper](https://www.microsoft.com/en-us/research/wp-content/uploads/2004/08/siggraph04-grabcut.pdf).
GrabCut is an "interactive foreground extraction \[technique\] using iterated graph cuts".[2]

The approach can be described as the following:

_Initialization:_ To assign each pixel of the image to either the foreground or the background, we 
first need the user to box out the foreground object. Then, we will use Gaussian mixtures to 
model the color distribution of the foreground and the background. We will initialize the
foreground Gaussian mixture using points inside of the box and the background Gaussian 
mixture from points outside of the box. All points outside of the box are tied to the background. 
Their assignment will not change.

_Iterative Optimization:_
1. Learn foreground and background Gaussian mixtures.
2. Construct a graph where we have all points inside the box plus a source and a sink as vertices.
    * All vertices that correspond to points in the image have edges to the source and the sink. 
    * They also have edges connected to vertices corresponding to neighboring points in the image. 
    * The edges connecting the source/sink and the point vertices have the log probability of
    the point given the foreground/background Gaussian mixture as their capacity.
    * The edges connecting vertices corresponding to neighboring points have a capacity that
gets larger if the distance between the two neighboring points is small.
Conceptually, we want to make the segmentation smooth, and we do not want to cut where the gradient is weak.
3. Apply min-cut to the graph. Points reachable from the source are assigned to the 
foreground. Other points are assigned to the background.
4. Repeat from step 1 until convergence.

_User Input:_ User can provide additional input (mark pixels red for foreground and blue for 
background), and we will assign those points according to user input and repeat iterative 
optimization. Points assigned according to user input will not change their assignment

You can lean more about GrabCut from the [paper](https://www.microsoft.com/en-us/research/wp-content/uploads/2004/08/siggraph04-grabcut.pdf).

#### GrabCut

<div style="text-align:center">
  <div style="display:inline-block;">
    <img src="/assets/images/2021-06-06/grabcut_1.png" width=160/>
  </div>
  <div style="display:inline-block;">
    <img src="/assets/images/2021-06-06/grabcut_1_result.png" width=160/>
  </div>
</div>

#### GrabCut with User Input

<div style="text-align:center">
  <div style="display:inline-block;">
    <img src="/assets/images/2021-06-06/grabcut_2.png" width=160/>
  </div>
  <div style="display:inline-block;">
    <img src="/assets/images/2021-06-06/grabcut_2_naive_result.png" width=160/>
  </div>
</div>
<div style="text-align:center">
  <div style="display:inline-block;">
    <img src="/assets/images/2021-06-06/grabcut_2_user_input.png" width=160/>
  </div>
  <div style="display:inline-block;">
    <img src="/assets/images/2021-06-06/grabcut_2_user_input_result.png" width=160/>
  </div>
</div>

[Back to ToC](#toc)

That's all of the projects! Thanks a lot!

* [1] [CS445 Spring 2021 Syllabus](https://courses.engr.illinois.edu/cs445/sp2021/cs445_syllabus_sp21.pdf)
* [2] [“GrabCut” — Interactive Foreground Extraction using Iterated Graph Cuts](https://www.microsoft.com/en-us/research/wp-content/uploads/2004/08/siggraph04-grabcut.pdf)

