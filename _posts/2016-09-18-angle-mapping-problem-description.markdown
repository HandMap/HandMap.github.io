---
layout: post
title: "Angle mapping problem description"
date: 2016-09-18
excerpt: I outline my interpretation of the angle mapping/calculation problem I'm currently working on for the project
tags:
- angle
---

## Introduction
---

One of the problems I'm currently looking to solve is how I'm going to be mapping and calculating the angular offset from the wrist that the hand is bending at. The reason for investigating this particular issue came about whilst speaking with a contacts from PMH and the OT department at Curtin University.

In the following post I'll do my best to outline the problem and state a few of my assumptions as to keep a good record of my workings.

### Assumption 1 - Cube Sensor
---

One of the key pieces of information I'm basing this description on is the idea that I'll be able to extract positional and gravitational data from the various sensors inside of the Blue box. The piece of data that will be most helpful is a measurement telling me what direction gravity is pulling downwards.

The figure below gives an idea of the data expected to be supplied.

{% capture fig_img %}
![Hand Gravity]({{ site.url }}/images/posts/2016-09-18/angle-gravity-hand.png)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>Gravitational data from box sensor</figcaption>
</figure>

### Assumption 2 - High Point on Hand
---

The next assumption I'll be making for now is that the peak point on the bent hand is where I'll be taking the centered measurement. If I use this assumption I should be able to begin calculating the angles using a method outlined in the image below:

{% capture fig_img %}
![Hand Gravity Transform]({{ site.url }}/images/posts/2016-09-18/angle-gravity-hand-aligned.png)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>Gravitational transform to centre point</figcaption>
</figure>

### Assumption 3 - Box Transformation
---

The third assumption is that if I take half the height of the box and use it to construct an imaginatry replica of the current box at a given position down the arm, I'll be able to use that as a reference point for my other angle. This angle would be the last piece of information I'd need to fairly accurately plot the bend in the hand/arm.

{% capture fig_img %}
![Box Transform]({{ site.url }}/images/posts/2016-09-18/angle-gravity-hand-box-transform.png)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>Box transform idea</figcaption>
</figure>

## Possible Issues
---

### Problem 1
---

Unknown scale could lead to an issue where I can't compute the angle because I don't have any way of calculating the distance

### Solution 1
---

Simply have a line(s) printed on the box with a length of exactly 1cm. This line could be used as a size/scaling reference point and used in the final calculations.

### Problem 2
---

This method only works side on

### Solution 2
---

The final implementation looks like it will require an image from the Top-down and Side-on. Means another method will be implemented for Size-on captures

### Problem 3
---

What if the highest point on the hand isn't the bend in the wrist?

### Solution 3
---

I don't think I can use the 'highest' point per se. More than likely it will actually be that contour mapping and feature detection methods will identify the Pisiform (pointy bone on wrist) and that will be considered the primary axis.

## Conclusion
---

The above concept doesn't really have much backing to it yet and I'll still need to do the maths and work out if it makes sense technically. I'd also like to cover the mapping of the hand from the Top-down method and identify how that will work.