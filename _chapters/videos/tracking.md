---
title: Tracking
keywords: (insert comma-separated keywords here)
order: 16 # Lecture number for 2020
---

_(Some introduction here)_


* [Feature Tracking](#feature-tracking)
    * [Problem Statement](#problem-statement)
    * [Challenges in Feature Tracking](#challenges-in-feature-tracking)
    * [Quality of Good Features](#quality-of-good-features)
    * [Motion Estimation Techniques](#motion-estimation-techniques)
* [Simple KLT Tracker](#simple-klt-tracker)
    * [Pipeline](#pipeline)
    * [Results](#results)
* [2D Transformations: Recap](#2d-transformations-recap)
    * [Types of 2D Transformations](#types-of-2d-transformations)
    * [Translation](#translation)
    * [Similarity Motion](#similarity-motion)
    * [Affine Motion](#affine-motion)
* [Iterative KLT Tracker](#iterative-klt-tracker)
    * [Problem Setting](#problem-setting)
    * [KLT Objective](#klt-objective)
    * [Mathematical Solution](#mathematical-solution)
    * [Interpretation of the H Matrix](#interpretation-of-the-h-matrix)
    * [Overall KLT Tracker Algorithm](#overall-klt-tracker-algorithm)
    * [KLT over Multiple Frames](#klt-over-multiple-frames)
    * [Challenges in Iterative KLT Tracker](#challenges-in-iterative-klt-tracker)



## Feature Tracking

### Problem Statement

Feature tracking takes an input a sequence of images, representing a video taken by a camera.

The goal is to not just detect keypoints in each frame, but to **follow** the points through the sequence.

Instead of treating each frame as a brand new image, we would like to keep track of **which** points from the previous frame represent the same object as the current frame.

_Single object tracking_ applies when we can make an assumption that each frame will contain the object we want to track exactly once.

_Multiple object tracking_ applies when there could be any number of objects in each frame, such as a crowd of people on a busy street.

Assumptions can also be made regarding the camera: is it fixed, or can it move about? Fixed camera is easier. Multiple cameras is also a possibility, given assumptions about where the cameras are in physical space relative to each other.

### Challenges in Feature Tracking

* We must determine which features _can_ be tracked.
* We must account for objects that aren't moving, but have their appearance changing over time.
  * For example, a stationary object could appear to be moving if a shadow is being cast over it, darkening it from one side to the other.
* We must correct drift, which is when small errors in the location of key points can accumulate over time
* We need to be able to introduce and remove some or all of the tracking points, as objects are permitted to leave the frame entirely, or reenter at a later stage.

### Quality of Good Features

To account for these challenges, the features from the images that we decide to use must be resilient in those ways.

For example, we do not want to track "ambiguous" locations, meaning large undifferentiated regions of the image that are hard to "anchor" to a specific location, such as arbitrary points along edges or faces, or smooth color gradients. We want to look at corners and sharp changes.

We can look back at the Harris method to find corners, and determine the quality of our keypoints from single images. For example, we can use Harris to get the corners of the very first frame, then track them from there.


### Motion Estimation Techniques

This is similar to, but not the same as optical flow. Optical flow takes the pixels of the image and follows them based on image brightness changes across time and space simultaneously. This method only tries to estimate the motion of small number of key points over many frames. Applying optical flow can help towards this goal, by combining corner detection on each frame with the optical flow of pixel motion from the previous frame.

Feature tracking has many applications, such as using the combined keypoint locations and motions to solve for all locations in 3d space, for simultaneous location and mapping in robotics.



## Simple KLT Tracker

### Pipeline

_(Some texts here)_

### Results

_(Some texts here)_




## 2D Transformations: Recap

### Types of 2D Transformations

_(Some texts here)_

### Translation

_(Some texts here)_

### Similarity Motion

![similarity img](https://github.com/Visininjr/cs131_notes_dev/blob/master/images/similarity.png?raw=true)

Recall similarity motion is a rigid motion that includes scaling and translation. This can be defined as follows:

$$
\begin{array}{l}
x^{\prime}=a x+b_{1} \\
y^{\prime}=a y+b_{2}
\end{array}
$$

The similarity transformation matrix W and parameters p are described as follows:

$$
\begin{array}{l}
W=\left(\begin{array}{ccc}
a & 0 & b_{1} \\
0 & a & b_{2}
\end{array}\right) \\
p=\left(\begin{array}{ccc}
a & b_{1} & b_{2}
\end{array}\right)^{T} \\
W(x ; p)=\left(\begin{array}{ccc}
a & 0 & b_{1} \\
0 & a & b_{2}
\end{array}\right)\left(\begin{array}{l}
x \\
y \\
1
\end{array}\right) \\
\frac{\partial W}{\partial p}(x ; p)=\left(\begin{array}{lll}
x & 0 & 1 \\
y & 0 & 1
\end{array}\right)
\end{array}
$$
The last line is the Jacobian of the similarity transformation.

### Affine Motion

![affine img](https://github.com/Visininjr/cs131_notes_dev/blob/master/images/affine.png?raw=true)

Recall that affine motion includes scaling, translation, and rotation. This can be defined as follows:

$$
\begin{array}{l}
x^{\prime}=a_{1} x+a_{2} y+b_{1} \\
y^{\prime}=a_{3} x+a_{3} y+b_{2}
\end{array}
$$

The affine transformation matrix W and parameters p are described as follows:

$$
\begin{aligned}
&W=\left(\begin{array}{lll}
a_{1} & a_{2} & b_{1} \\
a_{3} & a_{4} & b_{2}
\end{array}\right)\\
&\begin{array}{llllll}
p=\left(\begin{array}{llllll}
a_{1} & a_{2} & b_{1} & a_{3} & a_{4} & b_{2}
\end{array}\right)^{T}
\end{array}\\
&W(x ; p)=\left(\begin{array}{ccc}
a & a_{2} & b_{1} \\
a_{3} & a_{4} & b_{2}
\end{array}\right)\left(\begin{array}{l}
x \\
y \\
1
\end{array}\right)\\
&\frac{\partial W}{\partial p}(x ; p)=\left(\begin{array}{llllll}
x & y & 1 & 0 & 0 & 0 \\
0 & 0 & 0 & x & y & 1
\end{array}\right)
\end{aligned}
$$

The last line is the Jacobian of the affine transformation.

## Iterative KLT Tracker

### Problem Setting

_(Some texts here)_

### KLT Objective

_(Some texts here)_

### Mathematical Solution

In this section, we will derive a closed form approximation of $p$ that minimizes the KLT objective.

Since $p$ may be large, finding the minimum of $\displaystyle L = \sum_x \left[I(W(x;p)) - T(x)\right]^2$ may be quite challenging. 

Instead, we will break down $p$ into two components $p = p_0 + \Delta p$. In this case, $p_0$ and $\Delta p$ represents large and small (residual) motions respectively. We can fix $p_0$ by initializing it with our best guess of the motion, then solve for the small value $\Delta p$.

Now, we can use the Taylor series to approximate $L$.

$$
f(x + \Delta x) = f(x) + \Delta x \frac{\partial f}{\partial x} + \Delta x^2 \frac{\partial^2 f}{\partial x^2} + \dots
$$

By applying this Taylor approximation with the first two terms, we learn that

$$ 
\begin{align*}
L &= \sum_x \left[I(W(x; p_0 + \Delta p)) - T(x)\right]^2 \\
&\approx \sum_x \left[I(W(x;p_0)) + \nabla I \frac{\partial W}{\partial p} \Delta p - T(x)\right]^2 \\
\end{align*}
$$

in which $\displaystyle \nabla I = \begin{bmatrix} I_x & I_y \end{bmatrix}$ and $\displaystyle \frac{\partial W}{\partial p}$ can be pre-computed for affine motions, translations, and other transformations.

Afterwards, we aim to find $\displaystyle \arg\min_{\Delta p} \overline{L}$ where $\displaystyle \overline{L} = \sum_x \left[I(W(x;p_0)) + \nabla I \frac{\partial W}{\partial p} \Delta p - T(x)\right]^2$

Computing its derivative with respect to $\Delta p$ and setting it equal to $0$, we get

$$ 
\frac{\partial \overline{L}}{\partial \Delta p} = \sum_x \left[\nabla I \frac{\partial W}{\partial p}\right]^T \left[I(W(x;p_0)) + \nabla I \frac{\partial W}{\partial p} \Delta p - T(x)\right] = 0
$$

By solving for $\Delta p$, we learn that

$$
\Delta p = H^{-1} \sum_x \left[\nabla I \frac{\partial W}{\partial p}\right]^T \left[T(x) - I(W(x;p_0))\right]
$$

in which $\displaystyle H = \sum_x \left[\nabla I \frac{\partial W}{\partial p}\right]^T \left[\nabla I \frac{\partial W}{\partial p}\right]$ must be invertible.

### Interpretation of the H Matrix

Let's consider for translation motions. We know that

$$
\begin{eqnarray*}
\nabla I &=& \begin{bmatrix} I_x & I_y \end{bmatrix} \\
\frac{\partial W}{\partial p} &=& \begin{bmatrix} 1 & 0 \\ 0 & 1\end{bmatrix}
\end{eqnarray*}
$$

Thus, the $H$ matrix becomes

$$ 
\begin{align*}
H &= \sum_x \left[\nabla I \frac{\partial W}{\partial p}\right]^T \left[\nabla I \frac{\partial W}{\partial p}\right] \\
&= \sum_x \left[\begin{bmatrix} I_x & I_y \end{bmatrix} \begin{bmatrix} 1 & 0 \\ 0 & 1\end{bmatrix}\right]^T \left[\begin{bmatrix} I_x & I_y \end{bmatrix} \begin{bmatrix} 1 & 0 \\ 0 & 1\end{bmatrix}\right] \\
&= \sum_x \begin{bmatrix} I_x^2 & I_xI_y \\ I_xI_y & I_y^2\end{bmatrix}
\end{align*}
$$

This is the matrix for the Harris corner detector. We can see that H is easily invertible when both of its eigenvalues are large, which represents a corner in the input image. Therefore, corners in images are good features for calculating translation motions.

### Overall KLT Tracker Algorithm

_(Some texts here)_

### KLT over Multiple Frames

_(Some texts here)_

### Challenges in Iterative KLT Tracker

_(Some texts here)_




## References

_(Some references here)_
