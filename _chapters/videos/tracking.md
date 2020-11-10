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

_(Some texts here)_

### Challenges in Feature Tracking

_(Some texts here)_

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

_(Some texts here)_

### Affine Motion

_(Some texts here)_




## Iterative KLT Tracker

### Problem Setting

_(Some texts here)_

### KLT Objective

_(Some texts here)_

### Mathematical Solution

Next, we will derive a closed form approximation of $p$ that minimizes the KLT objective.

Since $p$ may be large, finding the minimum of $\displaystyle L = \sum_x \left[I(W(x;p)) - T(x)\right]^2$ may be quite challenging. 

Instead, we will break down $p$ into two components $p = p_0 + \Delta p$. In this case, $p_0$ and $\Delta p$ represents large and small residual motions respectively. We can fix $p_0$ by initializing our best guess of the motion, then solve for the small value $\Delta p$.

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

in which $\nabla I = [I_x \hspace{5pt} I_y]$ and $\displaystyle \frac{\partial W}{\partial p}$ can be pre-computed for affine motions, translations, and other transformations.

Next, we aim to find $\displaystyle \arg\min_{\Delta p} \tilde{L}$ where $\displaystyle \tilde{L} = \sum_x \left[I(W(x;p_0)) + \nabla I \frac{\partial W}{\partial p} \Delta p - T(x)\right]^2$

Computing its derivative with respect to $\Delta p$ and setting it equal to $0$, we get
$$ 
\frac{\partial \tilde{L}}{\partial \Delta p} = \sum_x \left[\nabla I \frac{\partial W}{\partial p}\right]^T \left[I(W(x;p_0)) + \nabla I \frac{\partial W}{\partial p} \Delta p - T(x)\right] = 0
$$

By solving for $\Delta p$, we learn that
$$
\Delta p = H^{-1} \sum_x \left[\nabla I \frac{\partial W}{\partial p}\right]^T \left[T(x) - I(W(x;p_0))\right]
$$

in which $\displaystyle H = \sum_x \left[\nabla I \frac{\partial W}{\partial p}\right]^T \left[\nabla I \frac{\partial W}{\partial p}\right]$ must be invertible.

### Interpretation of the H Matrix

_(Some texts here)_

### Overall KLT Tracker Algorithm

_(Some texts here)_

### KLT over Multiple Frames

_(Some texts here)_

### Challenges in Iterative KLT Tracker

_(Some texts here)_



## References

_(Some references here)_
