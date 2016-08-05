---
layout: post
title: "Getting started with OpenCV"
date: 2016-08-05
excerpt: I begin to investigate one of the most popular open source computer vision libraies, OpenCV
tags:
- project
- opencv
- ubuntu
---

## Introduction

I've decided to investigate some of the computer vision libaries that are already available that could possibly already do what I need; therefore saying me a lot of time not having to re-invent the wheel.

I had the idea to look into OpenCV when I noticed a particular repository trending on [Github](https://github.com/) the other day. The repo in question; maintained by [Raphael Monnerat](https://github.com/Shinao) was titled [SmartMirror](https://github.com/Shinao/SmartMirror) and is designed to turn a two-way mirror into a gesture controlled smart device.

{% capture fig_img %}
![Smart Mirror]({{ site.url }}/images/posts/2016-08-05/SmartMirror_DisplayMenu_Preview.gif)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>Raphael using Smart Mirror</figcaption>
</figure>

The potential for me to apply a similar algorithm to the one he was using to my project was almost too obvious, as it looked as though his implementation used some form of Hand Detection/Recognition. He even had a debugging tool packaged with his code that would display the gesture recognition information in real time while testing.

{% capture fig_img %}
![Smart Mirror Debug]({{ site.url }}/images/posts/2016-08-05/SmartMirror_Debug.png)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>Smart Mirror Debug</figcaption>
</figure>

## Installing OpenCV

My first task was to install OpenCV on a system that I could use to test the SmartMirror code. I span up an Ubuntu 16.04.1 instance and ran the following code to setup the latest OpenCV version on the system.

```bash
## Install pre-requisites
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get install build-essential
$ sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
$ sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev

## Change directory into a working folder
$ cd ~/Documents/

## Clone OpenCV repo and build from source
$ git clone https://github.com/opencv/opencv.git
$ cd opencv/
$ mkdir release
$ cd release/
$ cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local ..
$ make
$ sudo make install
```

Now that I had the latest version of OpenCV installed I needed to make sure it was working. I also decided that my language of choice; particularly during the initial testing phases would be Python.

```bash
## Change into the OpenCV samples directory
$ ~/Documents$ cd opencv/samples/python/

## Either run each tool separately or use demo.py to list all demos
$ python demo.py 
```

I had a play around with a number of the built in tools and was very impressed by the level of quality and detailed code each example provided. One of my personal favorites was the 'edge.py' example as I felt like it would provide a very good starting point for when I begin writing my hand detection method.

{% capture fig_img %}
![OpenCV Edge Detection]({{ site.url }}/images/posts/2016-08-05/OpenCV-Edgepy-test.png)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>OpenCV Canny Edge Detection</figcaption>
</figure>

## SmartMirror Analysis

Now that I had OpenCV installed I decided I would attempt to demo the SmartMirror hand detection code to see if it was as good as it looked like it was.

I started out by cloning the repository using git and locating the 'test.py' file mentioned in the repo README.md.

```bash
## Change into SmartMirror/Motion directory
$ git clone https://github.com/Shinao/SmartMirror.git
$ cd SmartMirror/Motion

## Execute the test.py script
$ python test.py
```

The example was very finicky to get working, The hand detection itself works very well when it isn't getting confused about the background lighting.

{% capture fig_img %}
![SmartMirror Hand Detection]({{ site.url }}/images/posts/2016-08-05/SmartMirror_Debug_Nathan_3slide.png)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>SmartMirror Hand Detection</figcaption>
</figure>

Difficulty assign, the implementing was detecting my palm, and there was even logic in place to handle hand movements (swiping in different directions).

{% capture fig_img %}
![SmartMirror Hand Detection]({{ site.url }}/images/posts/2016-08-05/SmartMirror_Debug_Nathan_gesture.png)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>SmartMirror Gesture</figcaption>
</figure>

## Summary

Today was a productive session that really got me thinking about the possibilities that OpenCV has to offer. Now that I know that what I want to achieve is very possible with the OpenCV libraries I believe my next step will be to learn the OpenCV frameworks from scratch. My goal for the next week is to build up a small library myself so that I can begin to understand how other peoples code works without having to guess/hack a solution together.

I picked up a digital copy of [Practical Python and OpenCV](https://www.pyimagesearch.com/practical-python-opencv/) by Adrian Rosebrock as I've had it recommended to me before as a great practical reasource for learning the in's and out's of OpenCV.

Until next time,

Nathan.