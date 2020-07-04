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
 //    	count = count + 1;
	// }else{
	// 	clearInterval(setIntervalID);
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
