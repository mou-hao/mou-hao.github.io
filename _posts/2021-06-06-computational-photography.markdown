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

(Todo)

### Project 4: Image Based Lighting <a name="project-4:-image-based-lighting"></a>

(Todo)

### Final Project: GrabCut <a name="final-project:-grabcut"></a>

(Todo)


* [1] [CS445 Spring 2021 Syllabus](https://courses.engr.illinois.edu/cs445/sp2021/cs445_syllabus_sp21.pdf)

