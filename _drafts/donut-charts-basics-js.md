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

Not really well-versed in Canvas. I resorted to guessing where changes are needed because of presseed for time. I didn't like this so I went ahead and studied basics online.

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

```javascript
ctx.beginPath();
ctx.arc(50, 75, 20, 0, Math.PI * 2, false);
ctx.stroke();
ctx.fillStyle = 'rgb(200, 0, 0)';
ctx.fill();
```

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

Use the percentages to calculate the start and end angles
Use different fillStyle to color the different sections of the graph.

### From Pie to Donut

Bigger circle and a smaller circle right smack in the middle of the big circle. Fill color of the smaller circle should be the same as the background color so as to make it look hollow

```javascript
// write code here
```

## Donut Chart in Chart.js

How to use Chart.js to create a chart

What Chart.js do given the options that we didn

What I needed was to display a text in the middle with a different color.
Changes done to create the smaller circle

## Futher Readings

- [MDN's Canvas Tutorial](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial)
- [Chart.js Documentation](http://www.chartjs.org/docs/)
