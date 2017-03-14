---
layout: post
title: "OpenCV colour tracking in Unity3D"
date: 2017-03-14
excerpt: Curiosity got the better of me and I decided I wanted to make sure I could easily build a small application with OpenCV that would deploy onto an Android/iOS mobile device
tags:
- opencv
- android
- ios
- unity3d
---

## Introduction
---

All the work up to this point has been written in a way that makes it fairly easy to port to different platforms. However I realise that I haven't properly tested whether or not I can get OpenCV working on the mobile platform.

This week I decided to see if I could get something up and running with OpenCV in Unity3D, and then deploy it to my Android device. This way I could tell whether I am being realistic with the scope of my project, and also whether or not mobile smartphones can iterate and process live camera video fast enough and without any latency.

## OpenCV for Unity3D
---

There's bads news and some less bad news... The bad news is that OpenCV as a platform, is just as difficult to get working on mobile as it is PC/Mac. The less bad news is that there is a [paid package](https://www.assetstore.unity3d.com/en/#!/content/21088) for OpenCV integration that I picked up a while ago on sale and abstracts out all the terrible integration difficulties while I'm just testing performance (Yay!)

I want to stress that this isn't a permanent decision by me, and I only really want to use this package to test that the OpenCV libraries don't overload my mobile device too badly.

### Colour tracking example
---

There's a neat demo that came packaged along side the OpenCV package for Unity3D that handles multiple object detection based on colour. The example can be tweeked to show how the device will handle a number of different thresholds including

MAX_NUMBER_OBJECTS:

```csharp
/// <summary>
/// max number of objects to be detected in frame
/// </summary>
const int MAX_NUM_OBJECTS = 50;
```

MIN_OBJECT_AREA:

```csharp
/// <summary>
/// minimum and maximum object area
/// </summary>
const int MIN_OBJECT_AREA = 20 * 20;
```

It defines a number of different colour thresholds using a ColourObject helper class

```csharp
ColorObject blue = new ColorObject ("blue");
ColorObject yellow = new ColorObject ("yellow");
ColorObject red = new ColorObject ("red");
ColorObject green = new ColorObject ("green");
```

The helper class simply takes the colour string and returns a ColorObject object containing some relevant details

```csharp
int xPos, yPos;
string type;
Scalar HSVmin, HSVmax;
Scalar Color;
```

The colours themselves are defined within the ColourObject constructor in a simple switch statement

```csharp
if (name == "blue") {

    //TODO: use "calibration mode" to find HSV min
    //and HSV max values

    setHSVmin (new Scalar (92, 0, 0));
    setHSVmax (new Scalar (124, 256, 256));

    //BGR value for Green:
    setColor (new Scalar (0, 0, 255));

}
if (name == "green") {

    //TODO: use "calibration mode" to find HSV min
    //and HSV max values

    setHSVmin (new Scalar (34, 50, 50));
    setHSVmax (new Scalar (80, 220, 200));

    //BGR value for Yellow:
    setColor (new Scalar (0, 255, 0));

}
if (name == "yellow") {

    //TODO: use "calibration mode" to find HSV min
    //and HSV max values

    setHSVmin (new Scalar (20, 124, 123));
    setHSVmax (new Scalar (30, 256, 256));

    //BGR value for Red:
    setColor (new Scalar (255, 255, 0));

}
if (name == "red") {

    //TODO: use "calibration mode" to find HSV min
    //and HSV max values

    setHSVmin (new Scalar (0, 200, 0));
    setHSVmax (new Scalar (19, 255, 255));

    //BGR value for Red:
    setColor (new Scalar (255, 0, 0));

}
```

The overall result is remarkably good and while it doesn't look like it's terribly accurate, that's just due to the original designers implementation being tuned to a different set of colour thresholds.

{% capture fig_img %}
![OpenCV Colour tracking in Unity3D]({{ site.url }}/images/posts/2017-03-14/opencv-unity-colour-tracking.png)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>OpenCV Colour tracking in Unity3D</figcaption>
</figure>

## What I learnt
---

There are a couple key things I learnt from this short experiment, and I feel as though I really need to describe each of the points with a bit of detail:

### Position in frame
---

I really like how the author of the library has created a class to handle the objects that it detects, which allowed him to store positional information about where in respect of the camera frame are the different objects.

```csharp
/// Here are the ints that store the location reletive to the screen size
int xPos, yPos;

string type;
Scalar HSVmin, HSVmax;
Scalar Color;
```

I plan to take this idea back to my Python and C++ code and see if I can come up with a less hackie solution for storing the points of interest that I am capturing

### Modularity
---

So far almost all my code have been reasonable size scripts with very little modularity. This was partially because of a lack of understand about Python classes and I really need to go back and clean up some of my implementations to be more portable.

### Processing overhead for mobile
---

The overhead is a non-issue. It seems like most modern devices will easily be able to handle the processing overhead associated with computer vision. In the example I used a 50 object limit, and in some cases found it happily hitting that threshold without even stuttering

{% capture fig_img %}
![OpenCV Colour tracking in Kitchen]({{ site.url }}/images/posts/2017-03-14/opencv-unity-colour-tracking-kitchen.png)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>OpenCV Colour tracking in Kitchen</figcaption>
</figure>

## References
---

OpenCV for Unity store page - [https://www.assetstore.unity3d.com/en/#!/content/21088](https://www.assetstore.unity3d.com/en/#!/content/21088)