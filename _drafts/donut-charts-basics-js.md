---
layout: post
title: Basics of Donut Charts in JS
tags: [javascript]
---

It is frustrating that even though we already have several open-source libraries, you will still have a requirement that none of the existing libraries support.

That's why it is important to know the basics of a language or framework feature that you are using. This is so that you'll be able to dig into the code when needed.

I was recently tasked to help in creating a POC that will replace an existing system.

One of the requirements was to create a donut chart that displays some text in the middle of the inner circle. Naturally, I looked for an open-source library that already has this feature (DRY).

I didn't find any but found that Chart.js generates charts. The library didn't have the feature though. I had to look inside the code.

## Circles to Pies

Not really well-versed in Canvas. I resorted to guessing where changes are needed because of pressed for time. I didn't like this so I went ahead and studied basics online.

Donut charts are just pie charts with a smaller circle in the middle. Pie charts are just overlapping partially filled-circles with different colors.  

### Canvas

To draw a shape in HTML, we will need to use the `<canvas>` element.
where we draw shapes just like an artist use a clothe for painting picture. fixed-size drawing surface.
It has a width and a height. with default of 300px (width) to 150px (height).
JavaScript used to draw the shapes.

### Context
Canvas has a rendering context where functions for drawing shapes can be called

```javascript
// get the canvas element
var canvas = document.getElementById('container');

// get the rendering context of the canvas
var ctx = canvas.getContext('2d');
```

Get the canvas DOM element and then retrieve the context by using the `getContext()` method. '2d' is used because we are just concerned with creating a circle.

### Drawing a Circle

To draw a circle, the context can be used.

<p data-height="268" data-theme-id="23199" data-slug-hash="GZOZKK" data-default-tab="html" data-user="pamlesleylu" class="codepen">See the Pen <a href="http://codepen.io/pamlesleylu/pen/GZOZKK/">Drawing a Circle</a> by Pamela Lu (<a href="http://codepen.io/pamlesleylu">@pamlesleylu</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

`beginPath()` - starts a new path. All succeeding drawing commands will be based on the call to beginPath().

`arc()` - draws an arc with the following parameters

1. the x coordinate of the center of the circle
2. the y coordinate of the center of the circle
3. the radius
4. the start angle
5. the end angle in radiance (e.g., 2 PI is 360 degrees, PI is 180 degrees).
6. is counterclockwise - if false, the drawing of circle is in a clockwise position from start angle to end angle

`stroke()` - draws the outline of the circles

`fillStyle` - property for the style used when filling the shape

`fill()` - colors the shape

### Circle to Pie

If you think about it, pie charts are just several unclosed circles with different starting and ending points. Each circle is a portioned according to how big the data its representing is against the total of all the data.

Using our previous knowledge of drawing a circle, we can use the same code but with changing start angle (4th parameter of the `arc()` method) and end angle (5th parameter of the `arc()` method).

<p data-height="268" data-theme-id="23199" data-slug-hash="aNqWZV" data-default-tab="result" data-user="pamlesleylu" class="codepen">See the Pen <a href="http://codepen.io/pamlesleylu/pen/aNqWZV/">Circle to Pie Graph</a> by Pamela Lu (<a href="http://codepen.io/pamlesleylu">@pamlesleylu</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

First calculate the sum of the all the data to get the whole piece. amount of each element in the data will show how large the portion of the graph is. This is the ratio of the data to the sum of all the data.

Loop will be created to draw each portion for each data. The starting angle is 0 and the ending angle will be the `data / sum of of all data` * the full angle of the full circle (which is `Math.PI * 2`).

For each loop use a different color to indicate a different data.

### From Pie to Donut

To make the pie chart look like a donut, the needed step is just to create a smaller circle right smack in the middle of the pie chart and filling it with the color of the background or canvas to give it a hollowed center effect.

<p data-height="268" data-theme-id="23199" data-slug-hash="aNqWMa" data-default-tab="result" data-user="pamlesleylu" class="codepen">See the Pen <a href="http://codepen.io/pamlesleylu/pen/aNqWMa/">Pie to Donut</a> by Pamela Lu (<a href="http://codepen.io/pamlesleylu">@pamlesleylu</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

Create a new circle with the x and y axes for the center the same as the the one in the pie graph. Give the new circle a smaller radius to make it smaller and then color it `white` to give it a color the same as the color of the canvas.

## Donut Chart in Chart.js

How to use Chart.js to create a chart

What Chart.js do given the options that we didn

What I needed was to display a text in the middle with a different color.
Changes done to create the smaller circle

## Futher Readings

- [MDN's Canvas Tutorial](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial)
- [Chart.js Documentation](http://www.chartjs.org/docs/)
