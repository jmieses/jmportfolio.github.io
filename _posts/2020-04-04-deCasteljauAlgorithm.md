---
title: "deCasteljau Algorithm"
date: 2020-04-04
toc: true
excerpt: "OpenGL, deCasteljau, Algorithm"
mathjax: true
classes: wide	
---


<svg id="mysvg" style="display:block; width:70%; height:20em; margin:0em auto; border:0.07em solid #808080"></svg>
<label for="degree">Degree:</label>
<input type="number" id="degree" name="degree" step="1" value="5" min="3">


<script>
var svg = document.querySelector('#mysvg');
var degree = document.getElementById("degree").value;

//dimension of svg
var svgRect = svg.getBoundingClientRect();
var svgw = svgRect.width;
var svgh = svgRect.height;

//center point of svg
var xcenter = svgw/2;
var ycenter = svgh/2;
var ctrlPts = [];
var rayCount = 20;

function randn_bm() {
    let u = 0, v = 0;
    while(u === 0) u = Math.random(); //Converting [0,1) to (0,1)
    while(v === 0) v = Math.random();
    let num = Math.sqrt( -2.0 * Math.log( u ) ) * Math.cos( 2.0 * Math.PI * v );
    //num = num / 10.0 + 0.5; // Translate to 0 -> 1
    if (num > 1 || num < 0) return randn_bm(); // resample between 0 and 1
    return num;
}

//http://stackoverflow.com/a/3642265/1869660
function makeSVGElement(tag, attrs) {
    var el= document.createElementNS('http://www.w3.org/2000/svg', tag);
    for (var k in attrs) {
        el.setAttribute(k, attrs[k]);
    }
    return el;
}

function rnPoints(){
    while(svg.firstChild != null)
        svg.removeChild(svg.firstChild);
    degree = document.getElementById("degree").value;
    for(var i = 0; i < degree; i++){
        var svgx = svgw * randn_bm();
        var svgy = svgh * randn_bm();
        ctrlPts.push({x : svgx, y : svgy});
        var circle = makeSVGElement('circle', { cx: ctrlPts[i].x,
                                        cy: ctrlPts[i].y,
                                        r: 5,
                                        stroke: 'red',
                                       'stroke-width': 2,
                                        fill: 'orange' });  
        svg.appendChild(circle);
    }
   
 // stop random point generation   
 //    if(count < 20){  
 //     count = count + 1;
    // }else{
    //  clearInterval(setIntervalID);
    // }
}

var count = 1;
var setIntervalID = setInterval(deCasteljau, 5000);

function deCasteljau(){
    rnPoints();
    var cpyCtrlPts = ctrlPts;
    var n = cpyCtrlPts.length;
    for(var u = 0.0; u <= 1.0; u += 0.001){ 
        for(var i = 1; i < n; i++){
            for(var j = 0; j < n - i; j++){
                cpyCtrlPts[j].x = (1.0-u) * cpyCtrlPts[j].x + u * cpyCtrlPts[j + 1].x
                cpyCtrlPts[j].y = (1.0-u) * cpyCtrlPts[j].y + u * cpyCtrlPts[j + 1].y
            }
        }
       var circle = makeSVGElement('circle', { cx: cpyCtrlPts[0].x,
                                        cy: cpyCtrlPts[0].y,
                                        r: 1,
                                        stroke: 'black',
                                       'stroke-width': 2,
                                        fill: 'orange' });  
       svg.appendChild(circle);
    }
    ctrlPts = [];

}

// I really enjoy working with curves since they give a lot of room for playing around 
// from a programming and mathematical perspective. The curve can be controlled by playing
// around with several variables which give a higher level of understanding on how this
// structure works.
</script>


## Introduction

The deCasteljau's algorithm introduces a way to create Bezier curves through the evaluation of Bernstein polynomials. A Bezier curve 
is a parametric polynomial curve that uses polynomials for its coordinates functions [1]. Consequently, an nth-degree Bezier curve is defined by


$$\textbf{C}(u) = \sum^n_{i=0}B_{i,n}(u)\textbf{P}_{i} \qquad  0 \leq u \leq 1. $$


The set $\\{\textbf{P}\\}$ are called control points, the $B_{i,n}(u)$ are Bernstein polynomials and $n$ is the degree of the curve.
The control points are the geometric coefficients of the Bezier curve and the Bernstein polynomials are basis functions. Therefore, 
the curve $\textbf{C(u)}$ can be expressed as a linear combination of the control points and Bernstein polynomials. Consequently, 
the deCasteljau's algorithm recursively iterates over the control points to produce a numerical stable Bezier curve.




Bernstein polynomials gives a formulation for building Bezier curves of any degree. The Bernstein polynomials $B_{i,n}$ are defined as

\begin{equation}
B_{i,n}(u) = \frac{n!}{i!(n-i)!}u^i(1-u)^{n-i} \qquad 0 \leq u \leq 1.
\end{equation}

However, the deCasteljau's algorithm avoids the direct calculation of factorials. Instead, it exploits the Bernstein polynomials property where
$B_{0,n}(0) = B_{n,n}(1) = 1$, and it produces a point on the curve by recursion. In addition, the partition of unity property $ \sum^n_{i=0}B_{i,n}(u) = 1 
\quad \forall u \in [0,1]$ 
ensures the interpolation between the control points.


## Example

For this example, let's create a quadratic Bezier curve. A quadratic Bezier curve represents the case for $n = 2$ in the definition above. Using the property
$B_{0,n}(0) = B_{n,n}(1) = 1$ the Bernstein polynomials take the following form


$$
B_{0,1}(u) = (1-u)B_{0,0}(u) + uB_{-1,0}(u) = 1 - u \\
B_{0,1}(u) = (1-u)B_{1,0}(u) + uB_{0,0}(u) = u \\
B_{0,1}(u) = (1-u)B_{0,1}(u) + uB_{-1,0}(u) = (1 - u)^2 \\
B_{0,1}(u) = (1-u)B_{0,0}(u) + uB_{-1,0}(u) = (1 - u)u + u(1-u) = 2u(1 - u) \\
B_{0,1}(u) = (1-u)B_{2,1}(u) + uB_{1,1}(u) = 1 - u
$$

This a direct result of using the recursive property of the basis functions. In addition, from the figure below shows the set of recursive dependency for the of 
$B_{1,2}$

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/bernstein-tree.PNG){: .align-center}



Then the curve formed by control points $\\{ \textbf{P}_0, \textbf{P}_1, \textbf{P}_2 \\}$ is

$$
\textbf{C(u)} = B_{0,2}P_0 + B_{1,2}P_1 + B_{2,2}P_2 
= (1-u)^2P_0 + 2u(1-u)P_1 + u^2P_2
$$


## Implementation

### OpenGL Setup


For drawing the curve I will be using modern OpenGL; the setup will use vertex and fragment shaders. I followed this [tutorial](https://learnopengl.com/) for the basic setup. 
In addition, the data will be computed with the CPU, then with pass this data to OpenGL drawing functions for the GPU to draw it. 

For the implementation, the algorithm will be encapsulated in a class. A curve class will contain the algorithm, so we could reuse it and implement other algorithms for curve
creation.

### deCasteljau Algorithm

We would like to display a 2D curve, therefore, having a struct with an x and y coordinates will come very handy. By defining a struct Point with two coordinates we can create a vector of points, which can then be used for the algorithm. Thus, it just makes sense to create a vector that holds a collection of 2D points for the control points. In addition, we will use the same data structure to copy the control points in a temporary vector of points, which will produce the points for the Bezier curve. 


```c++
#ifndef CURVE_H
#define CURVE_H

#include <vector>
struct Point {
    float x;
    float y;
};

class Curve{
public:
    Curve() = default;
    const std::vector<float>& deCasteljau(std::vector<float>& ctrl_pts);
private:
    void deCasteljau_Subroutine(float u);
    void Vector_To_Points(std::vector<float>& ctrl_pts);
    std::vector<float> m_curve;
    std::vector<Point> m_ctrl_pts;
    constexpr float m_res = 0.001f;

};
#endif
```


```c++
const std::vector<float>& Curve::deCasteljau(std::vector<float>& ctrl_pts){
    Vector_To_Points(ctrl_pts);

    if(!m_curve.empty()) m_curve.clear();

    for(float u = 0.0f; u < 1.0f; u += m_res){
        deCasteljau_Subroutine(u);
    }

    return m_curve;
}

void Curve::deCasteljau_Subroutine(float u){
    std::vector<Point> tmp_ctrl_pts(m_ctrl_pts);
    for(unsigned int i = 1; i < tmp_ctrl_pts.size(); i++){
        for(unsigned int j = 0; j < tmp_ctrl_pts.size() - i; j++){
            tmp_ctrl_pts[j].x = ((float)1. - u) * tmp_ctrl_pts[j].x + u * (tmp_ctrl_pts[j + 1].x);
            tmp_ctrl_pts[j].y = ((float)1. - u) * tmp_ctrl_pts[j].y + u * (tmp_ctrl_pts[j + 1].y);
        }
    }

    m_curve.emplace_back(tmp_ctrl_pts[0].x);
    m_curve.emplace_back(tmp_ctrl_pts[0].y);
    m_curve.emplace_back(0.0f);             // the curve lives in the xy plane (x, y, z = 0)
}
```


## References
[1][https://en.wikipedia.org/wiki/B%C3%A9zier_curve](https://en.wikipedia.org/wiki/B%C3%A9zier_curve)