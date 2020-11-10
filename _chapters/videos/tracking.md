---
title: Tracking
keywords: (insert comma-separated keywords here)
order: 16 # Lecture number for 2020
---

# Tracking

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
* We must correct drift, which is w

### Quality of Good Features

_(Some texts here)_

### Motion Estimation Techniques

_(Some texts here)_



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
$$ x' = ax + b_1 $$ \
$$ y' = ay + b_2 $$

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

### Affine Motion

![affine img](https://github.com/Visininjr/cs131_notes_dev/blob/master/images/affine.png?raw=true)

Recall that affine motion includes scaling, translation, and rotation. This can be defined as follows:
$$ x'=a_1 x+a_2 y+b_1 $$ \ 
$$ y'=a_3 x+a_3 y+b_2 $$

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

in which $\displaystyle \nabla I = [I_x \hspace{5pt} I_y]$ and $\displaystyle \frac{\partial W}{\partial p}$ can be pre-computed for affine motions, translations, and other transformations.

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

We know that $\displaystyle \nabla I = [I_x \hspace{5pt} I_y]$ and $\displaystyle \begin{bmatrix} 1 & 0 \\ 0 & 1\end{bmatrix}$ for translation motion. Thus, the $H$ matrix becomes

$$ 
\begin{align*}
H &= \sum_x \left[\nabla I \frac{\partial W}{\partial p}\right]^T \left[\nabla I \frac{\partial W}{\partial p}\right] \\
&= \sum_x \left[[I_x \hspace{5pt} I_y] \begin{bmatrix} 1 & 0 \\ 0 & 1\end{bmatrix}\right]^T \left[[I_x \hspace{5pt} I_y] \begin{bmatrix} 1 & 0 \\ 0 & 1\end{bmatrix}\right] \\
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
