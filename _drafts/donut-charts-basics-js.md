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

Canvas and context...

To draw a circle...

To draw a partially filled circles

To draw a half circle and another half circle...

## Donut Chart in Chart.js

How to use Chart.js to create a chart

What Chart.js do given the options that we didn

What I needed was to display a text in the middle with a different color.
Changes done to create the smaller circle

## Futher Readings

- [MDN's Canvas Tutorial](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial)
- [Chart.js Documentation](http://www.chartjs.org/docs/)
