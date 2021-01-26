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

The deCasteljau's algorithm introduces a way to create Bezier curves through the evaluation of Bernstein polynomial. A Bezier curve is a parametric polynomial curve that uses polynomials for its coordinates functions [1]. Consequently, an nth-degree Bezier curve is defined by


$$\textbf{C}(u) = \sum^n_{i=0}B_{i,n}(u)\textbf{P}_{i} \qquad  0 \leq u \leq 1. $$


The deCasteljau's algorithm recursively iterates over the control points to produces a numerical stable Bezier curve.


<p>The $\textbf{P}_i$ are called the control points, and they are the geometric coefficients of the Bezier curve.
The $B_{i,n}(u)$ are basis functions, therefore the curve $\textbf{C(u)}$ can be expressed as a linear combination of the control points, and these basis functions.
</p>

The $B_{i,n}$ are called Bernstein polynomials, and they are defined as
\begin{equation}
B_{i,n}(u) = \frac{n!}{i!(n-i)!}u^i(1-u)^{n-i} \qquad 0 \leq u \leq 1
\end{equation}
Bernstein polynomials gives a formulation for building Bezier curves of any degree.


## Example


Let's see the case for $n = 2$, the Bernstein basis functions takes the following form
$$B_{0,2} = (1-u)^2 \quad B_{1,2} = 2u(1-u) \quad B_{2,2}=u^2$$

Then the curve formed by control points $\\{ \textbf{P}_0, \textbf{P}_1, \textbf{P}_2 \\}$ is

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

We would like to display a 2D curve, therefore, having a struct with an x and y coordinates will come very handy. By defining a struct Point with two coordinates we can create a vector of points, which can then be used for the algorithm. Thus, it just makes sense to create a vector that holds a collection of 2D points for the control points. In addition, we will use the same data structure to copy the control points in a temporary vector of points, which will produce the points for the Bezier curve. 

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

    std::vector<Point> temp_control_points(control_points);
    
    for (unsigned int i = 1; i < control_points.size(); i++) {
        for (unsigned int j = 0; j < control_points.size() - i; j++) {
            temp_control_points[j].x = (1.0 - u) * temp_control_points[j].x + u * temp_control_points[j + 1].x;
            temp_control_points[j].y = (1.0 - u) * temp_control_points[j].y + u * temp_control_points[j + 1].y;
        }
    }  
    curve.C[0].x = temp_control_points[0].x;
    curve.C[0].y = temp_control_points[0].y;
}
```


## References
[1][https://en.wikipedia.org/wiki/B%C3%A9zier_curve](https://en.wikipedia.org/wiki/B%C3%A9zier_curve)