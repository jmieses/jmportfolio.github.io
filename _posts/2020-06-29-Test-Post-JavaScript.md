---
title: "Test Post JavaScript"
date: 2020-06-29
toc: true
excerpt: "Testing, JS"
mathjax: true
classes: wide	
---

Testing Post for JavaScript


<svg id="window" version="1.1" xmlns="http://www.w3.org/2000/svg" viewBox="-0.5 -0.5 1 1" style="display:block; width:70%; height:20em; margin:0em auto; border:0.07em solid #808080">
	<path d="" fill="#E8E8E8" stroke-width="0.006" stroke="#808080"/>
	<g fill="#909090"></g>
	<g fill="#FF0000"></g>
</svg>

<script type="text/javascript">
	var svg = document.getElementById("window");	

		// create the circle node, set attributes, and append it to the SVG node
	const circleNode = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
	circleNode.setAttributeNS(null, 'cx', '50');
	circleNode.setAttributeNS(null, 'cy', '50');
	circleNode.setAttributeNS(null, 'r', '40');
	circleNode.setAttributeNS(null, 'fill', 'blue');
	svgNode.appendChild(circleNode);
</script>