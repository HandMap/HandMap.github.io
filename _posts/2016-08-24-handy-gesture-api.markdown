---
layout: post
title: "'Handy' GestureAPI"
date: 2016-08-24
excerpt: I found a fantastic project by Vasu Agrawal that was designed as a Gesture tracking system. I investigate it's portability for my project
tags:
- opencv
- gestures
- api
---

## Introduction
---

Today while researching approaches for quick Hand mapping using contours I came across a fantastic example in the form of a simple API by a young man named [Vasu Agrawal](https://github.com/VasuAgrawal/GestureDetection). Not only was his code a wealth of interesting examples but he also included some fantastic documentation that he kept whilst working on his Gesture tracking project at Carnegie Mellon University.

My goal tonight is to utilize his API and explore how he has overcome some of the problems I've been faced with when implementing some of my image processing methods from the previous posts.

## Converting from OpenCV2.x to 3.x
---

One of the first problems I needed to overcome was relating to the version of OpenCV Vasu had used when working on this project a few years back. I'm targetting OpenCV version 3+ whilst his API was written for OpenCV2.x.

The first issue in the code came from Lines 15 and 16 in `GesturesApi.py`

```python
self.cap.set(cv2.cv.CV_CAP_PROP_FRAME_WIDTH, self.cameraWidth)
self.cap.set(cv2.cv.CV_CAP_PROP_FRAME_HEIGHT, self.cameraHeight)
```

Both cv.CV_CAP_PROP_FRAME_WIDTH and HEIGHT respectively are not valid variables in the new version of OpenCV. To correct this problem I referenced the API docs for the [VideoCapture.set() function](http://docs.opencv.org/3.1.0/d8/dfe/classcv_1_1VideoCapture.html) and found that I simply needed to make the following modifications to update support for OpenCV3.x

```python
self.cap.set(cv2.CAP_PROP_FRAME_WIDTH, self.cameraWidth)
self.cap.set(cv2.CAP_PROP_FRAME_HEIGHT, self.cameraHeight)
```

The second and last issue was with the findContours() method in `GesturesApi.py` on lines 118 through 120.

```python
self.contours, _ = cv2.findContours(self.thresholded.copy(),
                                    cv2.RETR_TREE,
                                    cv2.CHAIN_APPROX_SIMPLE)
```

Interestingly enough this was an issue I'd already dealt with previously so I knew the fix was to ammend a `_` before the `self.contours` return variable. This is because in OpenCV2.x findContours() would only return two variables whereas now in [OpenCV3.x](http://docs.opencv.org/3.0-beta/modules/imgproc/doc/structural_analysis_and_shape_descriptors.html?highlight=findcontours#cv2.findContours) we are returned the original input image along with the contours and hierarchy

```python
_, self.contours, _ = cv2.findContours(self.thresholded.copy(),
                                    cv2.RETR_TREE,
                                    cv2.CHAIN_APPROX_SIMPLE)
```

## Testing bundled Example
---

After ironing out the bugs from updating, I ran the code and was amazed by the results. Below is an example of what I was able to capture with my webcam facing my flat hand.

{% capture fig_img %}
![Flat Hand Detect]({{ site.url }}/images/posts/2016-08-24/nathan-hand-base-test.png)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>Flat Hand Detect</figcaption>
</figure>

The results were great! but there's a catch, It only works well in very specific conditions. For example, practically any light throws the entire process out of whack.

{% capture fig_img %}
![Poor Hand Detect]({{ site.url }}/images/posts/2016-08-24/nathan-hand-poor-test.png)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>Poor Hand Detect</figcaption>
</figure>

The final thing I tested was how it handled by Partners hand. She is living with cerebral palsy and more specifically deals with flexion, or abnormal bending at the wrist or of the fingers, due to muscle spasticity on a daily basis. This means her hand won't always be in a shape that is easily analysed, so I really wanted to know how the current algorithm handle the hand matching.

{% capture fig_img %}
![Flexion Hand Detect]({{ site.url }}/images/posts/2016-08-24/yhana-hand-cp-test.png)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>Flexion Hand Detect</figcaption>
</figure>

The results of the test were very interesting as it looks like the center point of the hand isn't calculated correctly due to the abrupt direction particular parts of the hand make. I do have the advantage of being able to utilize a small `lego brick style` censor that will be placed on the hand during 3D capture. So hopefully I'll be able to use this as a easy to identify point of reference.

## Conclusion
---

At this stage I'm really happy with the potential this API will offer. Huge thanks to [Vasu Agrawal](https://github.com/VasuAgrawal/GestureDetection) again, seriously awesome work. I'm managing my own fork of the code base [here](https://github.com/HandMap/GestureDetection) that anyone can contribute to if they want.

Plans for next time will be augment my own ideas with the GestureApi core library and also make sure I can use a small `lego-like` block as a reference point (haar classification will probably be the way I go).

### References
---

Spastic cerebral palsy - [https://www.cerebralpalsy.org.au/what-is-cerebral-palsy/types-of-cerebral-palsy/spastic-cerebral-palsy/](https://www.cerebralpalsy.org.au/what-is-cerebral-palsy/types-of-cerebral-palsy/spastic-cerebral-palsy/)

GestureDetection API - [https://github.com/VasuAgrawal/GestureDetection](https://github.com/VasuAgrawal/GestureDetection)