---
title: "deCasteljau Algorithm"
date: 2020-04-04
toc: true
excerpt: "OpenGL, deCasteljau, Algorithm"
mathjax: true	
---




## Introduction


The deCasteljau algorithm gives a way to construct Bezier curves with a very simple approach. By recursively iterating over the control points, we can obtain the Bezier in a very efficient manner, and without the use of factorials for the generation of the basis functions of the curve.

A Bezier curve is a parametric polynomial curve that uses polynomials for its coordinates functions [1]. Consequently, an nth-degree Bezier curve is defined by


$$\textbf{C}(u) = \sum^n_{i=0}B_{i,n}(u)\textbf{P}_{i} \qquad  0 \leq u \leq 1 $$



<p>The $\textbf{P}_i$ are called the control points, these are the geometric coefficients of the Bezier curve.
The $B_{i,n}(u)$ are basis functions, therefore the curve 
</p>


$\textbf{C(u)}$ can be expressed as a linear combination of the control points, and these basis functions.



The $B_{i,n}$ are called Bernstein polynomials, and they are defined as
\begin{equation}
B_{i,n}(u) = \frac{n!}{i!(n-i)!}u^i(1-u)^{n-i} \qquad 0 \leq u \leq 1
\end{equation}
Bernstein polynomials gives a formulation for building Bezier curves of any degree.


## Example


Let's see the case for $n = 2$, the Bernstein basis functions takes the following form
$$B_{0,2} = (1-u)^2 \quad B_{1,2} = 2u(1-u) \quad B_{2,2}=u^2$$
Then the curve formed by control points $\textbf{P}_i$ is

$$
\textbf{C(u)} = B_{0,2}P_0 + B_{1,2}P_1 + B_{2,2}P_2 
= (1-u)^2P_0 + 2u(1-u)P_1 + u^2P_2
$$

The Bezier basis function definition in (2) involve the computation of factorials that we will avoid. In the other hand, we can exploit the basis function recursive property, and introduce a computational friendly implementation of Bezier curve. This is the fundamental concept of the deCastelajau algorithm. Defining  $\textbf{B}_{i,n} \equiv 0$ if $i < 0$ or $i > n$. Based on this definition, and using the example above, we can illustrate the recursive mechanism

$$
B_{0,1}(u) = (1-u)B_{0,0}(u) + uB_{-1,0}(u) = 1 - u \\
B_{0,1}(u) = (1-u)B_{1,0}(u) + uB_{0,0}(u) = u \\
B_{0,1}(u) = (1-u)B_{0,1}(u) + uB_{-1,0}(u) = (1 - u)^2 \\
B_{0,1}(u) = (1-u)B_{0,0}(u) + uB_{-1,0}(u) = (1 - u)u + u(1-u) = 2u(1 - u) \\
B_{0,1}(u) = (1-u)B_{2,1}(u) + uB_{1,1}(u) = 1 - u
$$

then, 

$$
\textbf{C(u)} = B_{0,2}P_0 + B_{1,2}P_1 + B_{2,2}P_2
= (1-u)^2P_0 + 2u(1-u)P_1 + u^2P_2
$$

which is the same result as before. The implementation of the deCastelajau algorithm is straightforward.


## Implementation

### OpenGL Setup

To create a window I used [GLFW](https://www.glfw.org "GLFW's Homepage") library. As a baseline, I used this [example](https://www.glfw.org/documentation.html) in the site. It provides a code snippet to create a window, which is enough to test and display our Bezier curve.

Consequently, for the drawing I used the legacy OpenGL with glBegin(). For the purpose of this quick test/drawing it would be just fine.

### deCasteljau Algorithm

We would like to display a 2D curve, therefore, having a struct with an x and y coordinates will come very handy. By defining a struct Point with two coordinates we can create a vector of points, which can then be used for the algorithm. Thus, it just makes sense to create a vector that holds collection of 2D points. In addition, for our we create a vector of control points 

```c++
typedef struct Point {
    double x, y;
};

typedef struct Curve {
    std::vector<Point> C;
    Curve(size_t size) {
        C.resize(size);
    }
};

std::vector<Point> control_points =
{
    { 0.0f / 10.0f, -1.1f / 10.0f},
    { -2.0f / 10.0f, 8.3f / 10.0f},
    { 0.5f / 10.0f, 6.5f / 10.0f},
    { 5.1f / 10.0f, 4.7f / 10.0f},
    { 3.3f / 10.0f, 3.1f / 10.0f},
    { 1.4f / 10.0f, 7.5f / 10.0f},
    { 2.1f / 10.0f, 0.0f / 10.0f},
};

Curve curve(control_points.size());
```

```c++
void deCasteljau(double u) {

    std::vector<Point> temp_control_points;
    temp_control_points = control_points;
    auto temp_size = temp_control_points.size() - 1;
    
    for (int i = 1; i < temp_size; i++) {
        for (int j = 0; j < temp_size - i; j++) {
            int res = temp_size - i;
            temp_control_points[j].x = (1.0 - u) * temp_control_points[j].x + u * temp_control_points[j + 1].x;
            temp_control_points[j].y = (1.0 - u) * temp_control_points[j].y + u * temp_control_points[j + 1].y;
        }
    }
    
    curve.C[0].x = temp_control_points[0].x;
    curve.C[0].y = temp_control_points[0].y;

}
```