---
layout: post
title: Donut Charts Basics
tags: [javascript]
---

The availability of open-source libraries has certainly made the lives of us programmers easier. If we need to implement a new functionality, the first thing that we will do is look for existing libraries online. More often than not, we will be able to find a library that would suit our needs. There are just those rare times when the library that we'd find does not fully support all the requirements that we need. When this happens, it will be extremely helpful to have an understanding of basics of the program feature so that we will be able to extend the library as needed.

Recently, when we needed to implement a donut chart for one of our web applications, I looked for an open-source chart generating library. I found [Chart.js](http://www.chartjs.org) and it's pretty awesome. It can generate different chart types and renders them nicely through the use of animations. It just lacked one small feature that we needed. It did not have an option to control the background color and the label of the center of a donut chart.

I had to dig into the source code to add this feature and found out that I did not have sufficient knowledge Canvas--the component used by Chart.js to render the charts. I had to look for tutorials online and learn how to generate a simple donut chart. This step-by-step process of creating a donut chart will be written here.

### Canvas

A canvas in HTML is where shapes can be drawn via the use of lines, rectangles and arcs. To create one, use the `<canvas>` element inside your HTML file. This element has a width and height that you can specify. If both both properties are not specified, the defaults will be used which are 300px for the width and 150px for the height.

```HTML
<body>
  <canvas id="container">
  </canvas>
</body>
```

To draw the shapes inside the canvas, a rendering context must first be retrieved via the `getContext()` method of the canvas DOM element. Since we're interested in drawing a pie chart, the '2d' context can be used by passing it into the `getContext()` method.

```javascript
// get the canvas element
var canvas = document.getElementById('container');

// get the rendering context of the canvas
var ctx = canvas.getContext('2d');
```

### Drawing a Circle

After retrieving a handle for context, we can start drawing our shapes. The shape that we are interested in is the circle. To create the pie chart, we will be creating layers of unfinished circles.

Several functions are made available by the context to draw a circle. The most important is the `arc()` method which will create an arc with the specified radius and angle.

<p data-height="268" data-theme-id="23199" data-slug-hash="GZOZKK" data-default-tab="html" data-user="pamlesleylu" class="codepen">See the Pen <a href="http://codepen.io/pamlesleylu/pen/GZOZKK/">Drawing a Circle</a> by Pamela Lu (<a href="http://codepen.io/pamlesleylu">@pamlesleylu</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

* `beginPath()` - Always needs to be called when starting to draw inside the canvase. All succeeding drawing commands will be based on the call to `beginPath()`.

* `arc()` - draws an arc. The following parameters are used to determine the center of the circle, how big the circle is and if a full circle should be drawn:
  1. the x coordinate of the center of the circle
  2. the y coordinate of the center of the circle
  3. the radius
  4. the start angle
  5. the end angle in radiance (e.g., 2 PI is 360 degrees, PI is 180 degrees).
  6. counterclockwise - if false, the drawing of circle is in a clockwise position from start angle to end angle

* `stroke()` - draws the outline of the circles

* `fillStyle` - property for the style used when filling the shape

* `fill()` - colors the shape

### Circle to Pie

If pie charts are decomposed, we'd see that they are just several unclosed circles with different starting and ending points. Each circle is a partitioned according to how big the data it represents against the total of all the data.

Using our previous knowledge of drawing a circle, we can use the same code but with changing start angle (4th parameter of the `arc()` method) and end angle (5th parameter of the `arc()` method).

<p data-height="268" data-theme-id="23199" data-slug-hash="aNqWZV" data-default-tab="result" data-user="pamlesleylu" class="codepen">See the Pen <a href="http://codepen.io/pamlesleylu/pen/aNqWZV/">Circle to Pie Graph</a> by Pamela Lu (<a href="http://codepen.io/pamlesleylu">@pamlesleylu</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

The logic would be to

1. Calculate the sum of the all the data in the dataset
2. Get the ratio of each data against the sum
3. Loop through each data and determine start angle and end angle for each data.
  * start point should be where the last iteration left off
  * end point should show how big the data is in comparison with the total so it should use the computed ratio in the previous step and add it to the starting point as the offset. This should `data / sum of of all data` * the full angle of the full circle (which is `Math.PI * 2`) + start point.
4. Each partition in the pie chart should given a diffeernt color and `fillStyle` must be changed

### From Pie to Donut

The last step is just to make the pie chart look like a donut. This involves drawing a smaller circle in the middle of the pie chart and filling it with the color of the background or canvas to give it a hollowed center effect.

<p data-height="268" data-theme-id="23199" data-slug-hash="aNqWMa" data-default-tab="result" data-user="pamlesleylu" class="codepen">See the Pen <a href="http://codepen.io/pamlesleylu/pen/aNqWMa/">Pie to Donut</a> by Pamela Lu (<a href="http://codepen.io/pamlesleylu">@pamlesleylu</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

To do this,

1. Create a new smaller circle (i.e., smaller radius) and  use the same x and y axes of the bigger circle to have it draw in the same location
2. Specify the `fillStyle` of the smaller circle (i.e., give it a color similar to the canvas color which is `white`)

## Conclusion

By gaining a basic understanding of how the pie chart is drawn, I was able to extend the functionality. Sometimes it's important to not just use a library but also to learn from it.

## Futher Readings

- [MDN's Canvas Tutorial](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial)
- [Chart.js Documentation](http://www.chartjs.org/docs/)
