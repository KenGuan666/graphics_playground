# <p style="text-align: center;"> Image Handling with Filters and Frequencies </p>

<p style="text-align: center;"> <a href = https://inst.eecs.berkeley.edu/~cs194-26/fa20/hw/proj2/> CS194-26 Proj #2 </a>: Fun with Filters and Frequencies, Ken Guan </p>

&nbsp;

## Background
How much information does an image contain? While we are already familiar with the semantics of raw pixels, this project demonstrates a few ways to extract more information: convolutions, gradients, filters, and Fourier domain transform. Each section below corresponds to a task in the original project (linked above).

&nbsp;

## Edge Detection with Gradient
First, we use a simple gradient method for edge detection. We convolve the image with two 1D vectors: 

<img src="https://latex.codecogs.com/svg.latex?\Large&space;D_x = [1, -1]" title="D_x = [1, -1]" />; <img src="https://latex.codecogs.com/svg.latex?\Large&space;D_y = [1, -1]^T" title="D_y = [1, -1]^T" />

We then binarize the gradient image with a threshold on magnitude. With some trials and errors, threshold=0.15 produced the edge image below.

<img src="./data/cameraman.png" width=200 align='top'>
<img src="./out_images/1.1convdx.png" width=200 align='top'>
<img src="./out_images/1.1convdy.png" width=200 align='top'>
<img src="./out_images/1.1gradient.png" width=200 align='top'>

From left to right: Original image, image * Dx, image * Dy, binary gradient image.

&nbsp;

## Edge Detection and Gaussian Blur
To remove the noise edges detected (see the tiny white dots toward the bottom?), we apply a Gaussian smooth before running gradient edge detection.

<img src="./data/cameraman.png" width=200 align='top'>
<img src="./out_images/1.2convdx.png" width=200 align='top'>
<img src="./out_images/1.2convdy.png" width=200 align='top'>
<img src="./out_images/1.2gradient.png" width=200 align='top'>

By comparison, the detected edges become significantly wider after Gaussian Blur. There's also much fewer noise pixels. Note that since convolution is a linear operator, one can combine the two convolution operations into one. Below are the Derivative of Gaussian (DoG) filters, applying which on the original image yields the exact same result.

<img src="./out_images/1.2dogx.png" width=200 align='top'>
<img src="./out_images/1.2dogy.png" width=200 align='top'>

&nbsp;

## Image Straightening

We are now equipped with all the tools perform edge detection on any image. An interesting use case of edge detection is image straightening, where one could collect information about angles of edges in an image then rotate to maximize number of horizontal/vertical edges. Below are my results and their corresponding histogram of angle of edges:

<p style="text-align: center;">
<img src="./data/facade.jpg" width=200 align='top'> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <img src="./out_images/1.3facaderotated-img-3.jpg" width=200 align='top'>
</p>

<p style="text-align: center;">
<img src="./out_images/1.3facaderotate0.jpg" width=200 align='top'> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <img src="./out_images/1.3facaderotate-3.jpg" width=200 align='top'>
</p>

&nbsp;

<p style="text-align: center;">
<img src="./data/shanghaioutput.jpg" width=200 align='top'> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <img src="./out_images/1.3shanghaioutputrotated-img3.jpg" width=200 align='top'>
</p>

<p style="text-align: center;">
<img src="./out_images/1.3shanghaioutputrotate0.jpg" width=200 align='top'> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <img src="./out_images/1.3shanghaioutputrotate3.jpg" width=200 align='top'>
</p>

&nbsp;

<p style="text-align: center;">
<img src="./data/movieoutput.jpg" width=200 align='top'> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <img src="./out_images/1.3movieoutputrotated-img-3.jpg" width=200 align='top'>
</p>

<p style="text-align: center;">
<img src="./out_images/1.3movieoutputrotate0.jpg" width=200 align='top'> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <img src="./out_images/1.3movieoutputrotate-3.jpg" width=200 align='top'>
</p>

&nbsp;

<p style="text-align: center;">
<img src="./data/treeoutput.jpg" width=200 align='top'> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <img src="./out_images/1.3treeoutputrotated-img5.jpg" width=200 align='top'>
</p>

<p style="text-align: center;">
<img src="./out_images/1.3treeoutputrotate0.jpg" width=200 align='top'> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <img src="./out_images/1.3treeoutputrotate5.jpg" width=200 align='top'>
</p>

&nbsp;

One can see that, by rotating an image, we shift the histogram. The first two images are nicely aligned to maximize the number of vertical and horizontal edges (which correspond to -&pi;/2, 0, &pi;/2 in the histogram). The third also worked due to a large number of edges at -&pi;/2, but the 4th image is completely wrecked because the distribution of edges look mostly random, rahter than centered around stright angles.

&nbsp;

## Image Sharpening

Image Sharpening is a technique that emphasizes the high frequency portions of the image. We do so by adding to the image another layer of high frequency content, which we obtain from subtracting the low frequency layer from the image.

<p style="text-align: center;">
<img src="./data/taj.jpg" width=200 align='top'> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <img src="./out_images/2.1tajunsharped.jpg" width=200 align='top'>
</p>

<p style="text-align: center;">
<img src="./data/shanghaioutput.jpg" width=200 align='top'> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <img src="./out_images/2.1shanghaioutputunsharped.jpg" width=200 align='top'>
</p>

<p style="text-align: center;">
<img src="./data/movieoutput.jpg" width=200 align='top'> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <img src="./data/movielow-freq.jpg" width=200 align='top'> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <img src="./out_images/2.1movielow-frequnsharped.jpg" width=200 align='top'>
</p>

Both sharpening are done with &alpha; = 1. That is: f = f + &alpha;(f - f * G) = f(2I - G). Note that the edges of the images are standing out more, while the low frequency portions are relatively dimmer.

Note that for the image which was blurred then sharpened, the high-frequency details were still lost in the transition. Image sharpening never increases the amount of information.

&nbsp;

## Hybrid Images

This part uses a blending technique introduced by <a href = http://olivalab.mit.edu/publications/OlivaTorralb_Hybrid_Siggraph06.pdf> Olivia et al. (2006) </a>. We stack high frequency content from one image and low frequency content from another to produce different views at different distances.

<p style="text-align: center;">
<img src="./data/trump.png" width=400 align='top'> &nbsp; <img src="./out_images/fft-low.png" width=400 align='top'>
</p>

<p style="text-align: center;">
 <img src="./data/biden.png" width=400 align='top'> &nbsp; <img src="./out_images/fft-high.png" width=400 align='top'>
</p>

<p style="text-align: center;">
<img src="./out_images/2.2hybridtrump.png" width=400 align='top'> &nbsp; <img src="./out_images/fft-low-filter.png" width=400 align='top'> 
</p> 

<p style="text-align: center;">
<img src="./out_images/2.2hybridbiden.png" width=400 align='top'> &nbsp; <img src="./out_images/fft-high-filter.png" width=400 align='top'>
</p>

<p style="text-align: center;">
<img src="./out_images/2.2hybridtrumpbiden.png" width=400 align='top'> &nbsp; <img src="./out_images/2.2hybridcombine.png" width=400 align='top'>
</p>

&nbsp;

Some other examples: 
<p style="text-align: center;">
<img src="./data/hamst.png" width=250 align='top'> &nbsp; <img src="./data/guinea.png" width=250 align='top'> &nbsp; <img src="./out_images/2.2hybridhamstguinea.png" width=250 align='top'>
</p>

&nbsp;

<p style="text-align: center;">
<img src="./data/blastoise.png" width=250 align='top'> &nbsp; <img src="./data/squirtle.png" width=250 align='top'> &nbsp; <img src="./out_images/2.2hybridblastoisesquirtle.png" width=250 align='top'>
</p>

Note that the last example, where the squirtle (right) is on high frequency and the blastoise (left) on low, the combination is not as good because a bunch of low frequency features interfere with close-distance views, and that the high frequency seems to dominate even at far distance.

&nbsp;

## Gaussian and Laplacian Stack

Gaussian/Laplacian Stack is a technique that decomposes an image into different frequency bands. (Approximately &sigma; = 2, 4, 8, 16)

<p style="text-align: center;">
<img src="./out_images/2.3monastack.jpg" width=800 align='top'> 
</p>

<p style="text-align: center;">
<img src="./out_images/2.32.2hybridtrumpbidenstack.png" width=800 align='top'> 
</p>

&nbsp;

## Multiresolution Blending

Finally! In this part we blend up 2 images by blending them on multiple frequency bands.
<p style="text-align: center;">
<img src="./data/blackking.png" width=250 align='top'> &nbsp; <img src="./data/whiteking.png" width=250 align='top'> &nbsp; <img src="./out_images/2.4blendblackkingwhiteking.png" width=250 align='top'>
</p>

&nbsp;

<p style="text-align: center;">
<img src="./data/hamst.png" width=250 align='top'> &nbsp; <img src="./data/guinea.png" width=250 align='top'> &nbsp; <img src="./out_images/2.4blendguineahamst.png" width=250 align='top'>
</p>

In the last blend of a hamster (left) and a guinea pig (right), we used a special Sigmoid mask which gives a non-linear blend.

<p style="text-align: center;">
<img src="./out_images/masked20.png" width=250 align='top'> <img src="./out_images/masked10.png" width=250 align='top'> <img src="./out_images/masked30.png" width=250 align='top'>
</p>

&nbsp;

<p style="text-align: center;">
<img src="./out_images/masked21.png" width=250 align='top'> <img src="./out_images/masked11.png" width=250 align='top'> <img src="./out_images/masked31.png" width=250 align='top'>
</p>

&nbsp;

<p style="text-align: center;">
<img src="./out_images/masked22.png" width=250 align='top'> <img src="./out_images/masked12.png" width=250 align='top'> <img src="./out_images/masked32.png" width=250 align='top'>
</p>

&nbsp;

<p style="text-align: center;">
<img src="./out_images/masked23.png" width=250 align='top'> <img src="./out_images/masked13.png" width=250 align='top'> <img src="./out_images/masked33.png" width=250 align='top'>
</p>

&nbsp;

<p style="text-align: center;">
<img src="./out_images/masked24.png" width=250 align='top'> <img src="./out_images/masked14.png" width=250 align='top'> <img src="./out_images/masked34.png" width=250 align='top'>
</p>

&nbsp;