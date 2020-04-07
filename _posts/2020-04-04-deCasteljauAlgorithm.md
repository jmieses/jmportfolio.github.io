---
layout: posts
title: "deCasteljau Algorithm"
date: 2020-04-04
tags: [opengl, algorithm, c++]
header:
 image: "images/kelly-sikkema-cXkrqY2wFyc-unsplash4.png"
excerpt: "OpenGL, deCasteljau, Algorithm"

---

 # Introduction
    
 The deCasteljau algorithm gives a way to construct Bezier curves with a very simple approach. A Bezier curve is a parametric polynomial curve that uses polynomials for its coordinates functions [1]. Consequently, an nth-degree Bezier curve is defined by
 

 \begin{equation} 
   \textbf{C}(u) = \sum^n_{i=0}B_{i,n}(u)\textbf{P}_{i} \qquad  0 \leq u \leq 1
 \end{equation}
 
 The \(\textbf{P}_i\) are called the control points, these are the geometric coefficients of the Bezier curve. The \(B_{i,n}(u)\) are basis functions, therefore the curve \(\textbf{C(u)}\) can be expressed as a linear combination of the control points, and these basis functions.
 
 The \(B_{i,n}\) are called Bernstein polynomials, and they are defined as
 \begin{equation}
   B_{i,n}(u) = \frac{n!}{i!(n-i)!}u^i(1-u)^{n-i} \qquad 0 \leq u \leq 1
 \end{equation}
 Bernstein polynomials gives a formulation for building Bezier curves of any degree.
 ## Example
 Let's see the case for \(n = 2\), the Bernstein basis functions takes the following form
 $$B_{0,2} = (1-u)^2 \quad B_{1,2} = 2u(1-u) \quad B_{2,2}=u^2$$
 Then the curve formed by control points \(\textbf{P}_i\) is
 
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

  #### Implementation
  
The Bezier curve is implemented using the Decasteljau algorithm, and using OpenGL for rendering. The controls points are obtained from the user clicks on the OpenGL display. 
A Point object and a Curve struct serve to process the points. In addition, two global pointer to Curve object *C and *Q are created. The actual control points stored in *C and copied into *Q, so the curve doesn't deformed by erasing control points. 
