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
	for(var i = 0; i < 5; i++){
		var circle = makeSVGElement('circle', { cx: svgw * randn_bm(),
                                        cy: svgh * randn_bm(),
                                        r: 5,
                                        stroke: 'red',
                                       'stroke-width': 2,
                                        fill: 'orange' });	
		svg.appendChild(circle);
	}
    if(count < 20){	
    count = count + 1;
	}else{
	clearInterval(setIntervalID);
	}
}

var count = 1;
var setIntervalID = setInterval(rnPoints, 2000);

</script>
