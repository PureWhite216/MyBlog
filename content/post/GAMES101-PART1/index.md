+++
author = "NekoRAM7"
title = "GAMES101 Note(Part1)"
date = "2022-05-08"
description = "The note of GAMES101"
tags = [
    "CG",
    "GameDev"
]
categories = [
    "GAMES101"
]
series = ["GAMES101"]
aliases = ["migrate-from-jekyl"]
image = "Image.jpg"
+++

This article is the first part of the GAMES101 note, including `Overview of CG`, `Linear Algebra` and `Transformation Matrix`
<!--more-->


## 1. Overview of CG
### 1.1 A simple standard to judge the CG tech - Brightness
The game with great **Globoal Illumination** will have a brighter scene.
### 1.2 Main Content
#### 1.2.1 Rasterization
Display geometry in 3D space on 2D screen.
#### 1.2.2 Curves and Meshes
#### 1.2.3 Ray Tracing
#### 1.2.4 Animation/Simulation

## 2. Linear Algebra
### 2.1 Vector
1. length, direction
2. unitization, addition and subtraction
3. dot product, cross product

#### The uses of Cross Product
1. Judge the relative direction of two vectors
2. Judge if the vector is inside or outside an area
### 2.2 Matrix
1. multiplication
2. associative, distributive 
3. transpose
4. identity matrix, inverse matrix

## 3. Transformation
### 3.1 Scale
$$\begin{bmatrix}
x&0&0\\
0&y&0\\
0&0&z\\
\end{bmatrix}$$
### 3.2 Reflection
#### Symmetric about the yz plane
$$\begin{bmatrix}
-1&0&0\\
0&1&0\\
0&0&1\\
\end{bmatrix}$$
### 3.3 Shear
#### 2D
$$\begin{bmatrix}
1&a\\
0&1
\end{bmatrix}$$
### 3.4 Rotation
#### 2D
$$
\begin{bmatrix}
cos\theta&-sin\theta\\
sin\theta&cos\theta
\end{bmatrix}
$$
#### 3D
$$
Rx(\alpha)=
\begin{bmatrix}
1&0&0&0\\
0&cos\alpha&-sin\alpha&0\\
0&sin\alpha&cos\alpha&0\\
0&0&0&1\\
\end{bmatrix}  
$$
$$
Ry(\alpha)=
\begin{bmatrix}
cos\alpha&0&sin\alpha&0\\
0&1&0&0\\
-sin\alpha&0&cos\alpha&0\\
0&0&0&1\\
\end{bmatrix}
$$
$$
Rz(\alpha)=
\begin{bmatrix}
cos\alpha&-sin\alpha&0&0\\
sin\alpha&cos\alpha&0&0\\
0&0&1&0\\
0&0&0&1\\
\end{bmatrix}
$$
##### Rodrigues' Rotation Formula
### 3.5 Homogenous Coordinates
#### 2D Point
$$
\begin{bmatrix}
x\\y\\1
\end{bmatrix}
$$
#### 3D Point
$$
\begin{bmatrix}
x\\y\\z\\1
\end{bmatrix}
$$
#### 2D Vector
$$
\begin{bmatrix}
x\\y\\0
\end{bmatrix}
$$
#### 3D Vector
$$
\begin{bmatrix}
x\\y\\z\\0
\end{bmatrix}
$$
#### 2D Translation
$$
\begin{bmatrix}
x'\\y'\\w'
\end{bmatrix}
=

\begin{bmatrix}
1&0&a\\
0&1&b\\
0&0&1\\
\end{bmatrix}

\begin{bmatrix}
x\\y\\1
\end{bmatrix}

=

\begin{bmatrix}
x+a\\y+b\\1
\end{bmatrix}
$$


#### Operation
1. vector + vector = vector
2. vector + point = point
3. point - point = vector
4. point + point = mid point
$$
\begin{bmatrix}
x\\y\\z\\w
\end{bmatrix}
=
\begin{bmatrix}
x/w\\y/w\\z/w\\1
\end{bmatrix}
$$

### 3.6 Affine Transformation
#### Affine map = Linear map + Translation
$$
\begin{bmatrix}
x'\\y'\\z'
\end{bmatrix}
=
\begin{bmatrix}
a&b&c\\
d&e&f\\
g&h&i\\
\end{bmatrix}
\begin{bmatrix}
x\\y\\z
\end{bmatrix}
+
\begin{bmatrix}
tx\\ty\\tz
\end{bmatrix}
$$
#### Using Homogenous Coordinates:
$$
\begin{bmatrix}
x'\\y'\\z'\\1
\end{bmatrix}
=
\begin{bmatrix}
a&b&c&tx\\
d&e&f&ty\\
g&h&i&tz\\
0&0&0&1\\
\end{bmatrix}
\begin{bmatrix}
x\\y\\z\\1
\end{bmatrix}
$$

### 3.7 Compose and Decompose Complex Transforms

### 3.8 View/Camera Transformation

#### MVP
$$
Model\rarr View\rarr Projection 
$$

#### How to define a camera
1. Positoin
2. Look at Direction 
3. Up Direction

#### View Matrix
$$
M_{view}=R_{view}T_{view}
$$
$$
R_{view}^{-1}=R_{view}^{T}
$$

#### Projection
##### Perspective and Orthographic






