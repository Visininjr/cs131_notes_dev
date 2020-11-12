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

<p align="center">
  <img src="https://github.com/Visininjr/cs131_notes_dev/blob/master/images/feature-tracking.jpg?raw=true" width="600">
  <br />
  <em>
    Feature Tracking [2]
  </em>
</p>

<!-- ![feature tracking img](https://github.com/Visininjr/cs131_notes_dev/blob/master/images/feature-tracking.jpg?raw=true)  -->

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

<p align="center">
  <img src="https://github.com/Visininjr/cs131_notes_dev/blob/master/images/seq_initial.gif?raw=true" width="200">
  <img src="https://github.com/Visininjr/cs131_notes_dev/blob/master/images/seq_tracked_1.gif?raw=true" width="200">
  <img src="https://github.com/Visininjr/cs131_notes_dev/blob/master/images/seq_features_1.gif?raw=true" width="200">
  <br />
  <em>
    Feature Tracking [3]
  </em>
</p>

<!-- ![feature tracking 2 img](https://github.com/Visininjr/cs131_notes_dev/blob/master/images/seq_initial.gif?raw=true) 

![feature tracking 3 img](https://github.com/Visininjr/cs131_notes_dev/blob/master/images/seq_tracked_1.gif?raw=true) 

![feature tracking 4 img](https://github.com/Visininjr/cs131_notes_dev/blob/master/images/seq_features_1.gif?raw=true)  -->


## Simple KLT Tracker

The name “KLT” is derived from the names of its authors Kenade, Lucas, and Tomasi. It is an approach to feature extraction. These are the steps for the algorithm:


### Pipeline

1. First, we want to find a good point to track. For instance, we can accomplish this by running a Harris corner detector and selecting some corners in the image.
1. Second, for every corner we detected in the first frame, we will compute the motion of that corner in the consecutive frames. We can accomplish this by using optical flow to calculate how the point is moving through time.
1. Then we can link the motion vectors we obtain from optical flow in successive frames to get a full track for each Harris point. 
1. Because we may lose some points due to occlusion, we may want to introduce new Harris points by re-running the Harris detector every so often.
1. Finally, we keep tracking new and old Harris points using steps 1-3.

<p align="center">
  <img src="https://github.com/Visininjr/cs131_notes_dev/blob/master/images/klt_cars.gif?raw=true" width="600">
  <br />
  <em>
    KLT [4, 5]
  </em>
</p>

<p align="center">
  <img src="https://github.com/Visininjr/cs131_notes_dev/blob/master/images/klt_movement.gif?raw=true" width="600">
  <br />
  <em>
    KLT [4, 5]
  </em>
</p>

<!-- ![klt img](https://github.com/Visininjr/cs131_notes_dev/blob/master/images/klt_cars.gif?raw=true) 

![klt 2 img](https://github.com/Visininjr/cs131_notes_dev/blob/master/images/klt_movement.gif?raw=true)  -->


## 2D Transformations: Recap

### Types of 2D Transformations

<p align="center">
  <img src="https://github.com/Visininjr/cs131_notes_dev/blob/master/images/2d_transforms.jpg?raw=true" width="600">
  <br />
  <em>
    Types of 2D Transformations [1]
  </em>
</p>

<!-- ![2d transformations img](https://github.com/Visininjr/cs131_notes_dev/blob/master/images/2d_transforms.jpg?raw=true) -->

* Translation
  * Translation
* Euclidean
  * Rotation
  * Translation
  * Reflection
* Similarity
  * Rotation
  * Translation
  * Scale
* Affine
  * Rotation
  * Translation
  * Scale
  * Shear
* Projective
  * Anything (does not even preserve parallelism)


### Translation

<p align="center">
  <img src="https://github.com/Visininjr/cs131_notes_dev/blob/master/images/translation.jpg?raw=true" width="400">
  <br />
  <em>
    Translation [1]
  </em>
</p>

<!-- ![translation img](https://github.com/Visininjr/cs131_notes_dev/blob/master/images/translation.jpg?raw=true) -->

$$
\begin{eqnarray*}
x^{\prime}&=& x+b_{1} \\
y^{\prime}&=&y+b_{2}\\
\begin{bmatrix}
x'\\
y'
\end{bmatrix}&=&\begin{bmatrix}
1 & 0 & b_{1} \\
0 & 1 & b_{2}
\end{bmatrix}\begin{bmatrix}
x \\
y \\
1
\end{bmatrix}\\
p&=&\begin{bmatrix}
b_{1} \\ b_{2}
\end{bmatrix}
\\
\frac{\partial W}{\partial p}(x ; p)&=&\begin{bmatrix}
1 & 0 \\
0 & 1
\end{bmatrix}
\end{eqnarray*}
$$

### Similarity Motion

<p align="center">
  <img src="https://github.com/Visininjr/cs131_notes_dev/blob/master/images/similarity.png?raw=true" width="500">
  <br />
  <em>
    Similarity [1]
  </em>
</p>

<!-- ![similarity img](https://github.com/Visininjr/cs131_notes_dev/blob/master/images/similarity.png?raw=true) -->

Recall similarity motion is a rigid motion that includes scaling and translation. This can be defined as follows:

$$
\begin{eqnarray*}
x^{\prime}&=&a x+b_{1} \\
y^{\prime}&=&a y+b_{2}
\end{eqnarray*}
$$

The similarity transformation matrix W and parameters p are described as follows:

$$
\begin{eqnarray*}
W&=&\begin{bmatrix}
a & 0 & b_{1} \\
0 & a & b_{2}
\end{bmatrix}\\
p&=&\begin{bmatrix}
a & b_{1} & b_{2}
\end{bmatrix}^T\\
W(x ; p)&=&\begin{bmatrix}
a & 0 & b_{1} \\
0 & a & b_{2}
\end{bmatrix}\begin{bmatrix}
x \\
y \\
1
\end{bmatrix}\\
\frac{\partial W}{\partial p}(x ; p)&=&\begin{bmatrix}
x & 0 & 1 \\
y & 0 & 1
\end{bmatrix}
\end{eqnarray*}
$$

The last line is the Jacobian of the similarity transformation.

### Affine Motion

<p align="center">
  <img src="https://github.com/Visininjr/cs131_notes_dev/blob/master/images/affine.png?raw=true" width="500">
  <br />
  <em>
    Affine [1]
  </em>
</p>

<!-- ![affine img](https://github.com/Visininjr/cs131_notes_dev/blob/master/images/affine.png?raw=true) -->

Recall that affine motion includes scaling, translation, and rotation. This can be defined as follows:

$$
\begin{eqnarray*}
x^{\prime}&=&a_{1} x+a_{2} y+b_{1} \\
y^{\prime}&=&a_{3} x+a_{3} y+b_{2}
\end{eqnarray*}
$$

The affine transformation matrix W and parameters p are described as follows:

$$
\begin{eqnarray*}
W&=&\begin{bmatrix}
a_{1} & a_{2} & b_{1} \\
a_{3} & a_{4} & b_{2}
\end{bmatrix}\\
p&=&\begin{bmatrix}
a_{1} & a_{2} & b_{1} & a_{3} & a_{4} & b_{2}
\end{bmatrix}^T\\
W(x ; p)&=&\begin{bmatrix}
a & a_{2} & b_{1} \\
a_{3} & a_{4} & b_{2}
\end{bmatrix}\begin{bmatrix}
x \\
y \\
1
\end{bmatrix}\\
\frac{\partial W}{\partial p}(x ; p)&=&\begin{bmatrix}
x & y & 1 & 0 & 0 & 0 \\
0 & 0 & 0 & x & y & 1
\end{bmatrix}
\end{eqnarray*}
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

in which $\displaystyle \nabla I = \begin{bmatrix} I_x & I_y \end{bmatrix}$ and $\displaystyle \frac{\partial W}{\partial p}$ can be pre-computed for affine motions, translation motions, and other transformations.

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

Let's consider translation motions. We know that

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

Given our formula for $\Delta p$, this is the pseudocode for the algorithm.

Given the features from Harris detector:

1. Initialize $p_0$.
2. Compute the initial templates $T(x)$ for each feature.
3. Transform the features in the image $I$ with $W(x;p_0)$.
4. Measure the error $\displaystyle I(W(x;p_0))-T(x)$.
5. Compute the image gradients $\displaystyle \nabla I = \begin{bmatrix} I_x & I_y \end{bmatrix}$.
6. Evaluate the Jacobian $\displaystyle \frac{\delta W}{\delta p}$.
7. Compute the steepest descent $\displaystyle \nabla I \frac{\delta W}{\delta p}$.
8. Compute Inverse Hessian $H^{-1}$.
9. Calculate the change in parameters $\Delta p$.
10. Update parameters $\displaystyle p_0 = p_0+\Delta p$.
11. Repeat steps 3 to 10 until $\Delta p$ is small.


### KLT over Multiple Frames

We can extend this over multiple frames. This is because once we find a transformation between two consecutive frames, you can repeat this process for every new frame that comes in. Run the Harris detector every so often ($15-20$ frames) to replenish features.

### Challenges in Iterative KLT Tracker

When implementing this algorithm, there are a few key issues to consider:
* Window size (size of neighborhood/template around $x$)
    * Small window more sensitive to noise and may miss larger motions (if we don't use a pyramid).
    * Large window more likely to cross an occlusion boundary, making the signal confusing to the algorithm (also, it is slower).
    * Most implementations stay between the range of $15 \times 15$ to $31 \times 31$ for neighborhoods.
* Weighting the window
    - Common to apply weights so that center matters more (e.g. with Gaussian weighting to give more emphasis to center pixels.)




## References

[1] http://vision.stanford.edu/teaching/cs131_fall1920/slides/18_tracking.pdf

[2] https://web.yonsei.ac.kr/jksuhr/articles/Kanade-Lucas-Tomasi%20Tracker.pdf

[3] http://www.vision.caltech.edu/bouguetj/Motion/navigation.html

[4] https://cecas.clemson.edu/~stb/klt/lucas_bruce_d_1981_1.pdf

[5] https://cecas.clemson.edu/~stb/klt/tomasi-kanade-techreport-1991.pdf
