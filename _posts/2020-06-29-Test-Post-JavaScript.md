---
title: "Test Post JavaScript"
date: 2020-06-29
toc: true
excerpt: "Testing, JS"
mathjax: true
classes: wide	
---

Testing Post for JavaScript


<svg id="mysvg" style="display:block; width:70%; height:20em; margin:0em auto; border:0.07em solid #808080"></svg>
<script>
var svg = document.querySelector('#mysvg');

//dimension of svg
var svgRect = svg.getBoundingClientRect();
var svgw = svgRect.width;
var svgh = svgRect.height;

//center point of svg
var xcenter = svgw/2;
var ycenter = svgh/2;

var rayCount = 20;


//http://stackoverflow.com/a/3642265/1869660
function makeSVGElement(tag, attrs) {
    var el= document.createElementNS('http://www.w3.org/2000/svg', tag);
    for (var k in attrs) {
        el.setAttribute(k, attrs[k]);
    }
    return el;
}

var circle = makeSVGElement('circle', { cx: xcenter,
                                        cy: ycenter,
                                        r: 10,
                                        stroke: 'red',
                                       'stroke-width': 2,
                                        fill: 'orange' });
svg.appendChild(circle);

var rayAngle = 360/rayCount;
for(var i=0; i<rayCount; i++) {
    var path = makeSVGElement('path', { d: 'M' + [xcenter, ycenter] + ' h250',
                                        stroke: 'orange',
                                       'stroke-width': 1,
                                        transform: 'rotate(' + [(rayAngle*i), xcenter, ycenter] + ')' });
    svg.appendChild(path);
}

</script>