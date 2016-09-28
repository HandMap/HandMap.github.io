---
layout: post
title: "Convex hull and contours"
date: 2016-09-28
excerpt: I use my newly functioning C++ binding to create a simple convex hull around a detected hand
tags:
- opencv
- c++
- convex hull
- contours
---

## Introduction
---

In light of the recent C++ binding success I had the other night, I decided today I would have a crack at a couple hand detectors written in C++. My goal today was to set in stone a reasonable method I could use to track and map the contours on a hand in real time.

Once I have these contours I would like to begin working on a way to find and mark the middle point of the hand.

## Setting up CLion CMake file
---

The first small problem I ran into was I needed to map out the newly installed C++ OpenCV libraries in CLion (though this step is agnostic between IDEs).

I followed a really helpful example provided by [mstfldmr](https://github.com/Homebrew/homebrew-science/issues/2466#issuecomment-148958374) that resulted in a CMakeLists.txt file with the following build instructions:

```bash
cmake_minimum_required(VERSION 2.8)
project(OpenCV-test)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
set(CMAKE_PREFIX_PATH "/usr/local/opt/opencv3/share/OpenCV")

set(OpenCV_INCLUDE_DIRS "/usr/local/opt/opencv3/include")
set(OpenCV_LIBS "/usr/local/opt/opencv3/lib")

set(SOURCE_FILES condefects.cpp)

# Find OpenCV, you may need to set OpenCV_DIR variable
# to the absolute path to the directory containing OpenCVConfig.cmake file
# via the command line or GUI
find_package(OpenCV REQUIRED)

# If the package has been found, several variables will
# be set, you can find the full list with descriptions
# in the OpenCVConfig.cmake file.
# Print some message showing some of them
message(STATUS "OpenCV library status:")
message(STATUS "    version: ${OpenCV_VERSION}")
message(STATUS "    libraries: ${OpenCV_LIBS}")
message(STATUS "    include path: ${OpenCV_INCLUDE_DIRS}")

# Add OpenCV headers location to your include paths
include_directories(${OpenCV_INCLUDE_DIRS})

# Declare the executable target built from your sources
add_executable(OpenCV-test condefects.cpp)

# Link your application with OpenCV librarcies
target_link_libraries(OpenCV-test ${OpenCV_LIBS})
```

NOTE: `condefects.cpp` is the C++ source file I'm compiling

Once I'd confirmed that I could indeed compile my C++ code and also reference the copencv2 libraries via the include headings I began the next step.

## HSV Calibration
---

In order to manually calibrate the HSV values associated with my tracker I used the [following code](https://drive.google.com/file/d/0B1ZBV653pn33dFFpdm5lMHdPbVU/view) from [this video](https://www.youtube.com/watch?v=DEHk-5xbJhU).

The GUI for the calibration can be seen in the following image:

{% capture fig_img %}
![HSV Calibration]({{ site.url }}/images/posts/2016-09-28/convex-hull-hsv-config.png)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>HSV Calibration GUI</figcaption>
</figure>

Using this method I was able to manually manipulate the HSV thresholds until the components (hand) that I wanted to capture was the main thing displayed in the image.

{% capture fig_img %}
![HSV Threshold]({{ site.url }}/images/posts/2016-09-28/hand-detect-hsv-thresholds.png)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>HSV Threshold output</figcaption>
</figure>

## Contour code
---

Below are the two code blocks used to show the contours and limit the convex defect set.

```cpp
void showimgcontours(Mat &threshedimg, Mat &original)
{
    vector<vector<Point> > contours;
    vector<Vec4i> hierarchy;
    int largest_area = 0;
    int largest_contour_index = 0;

    findContours(threshedimg, contours, hierarchy, CV_RETR_TREE, CV_CHAIN_APPROX_SIMPLE);

    /// Find the convex hull,contours and defects for each contour
    vector<vector<Point> >hull(contours.size());
    vector<vector<int> >inthull(contours.size());
    vector<vector<Vec4i> >defects(contours.size());
    for (int i = 0; i < contours.size(); i++)
    {
        convexHull(Mat(contours[i]), hull[i], false);
        convexHull(Mat(contours[i]), inthull[i], false);
        if (inthull[i].size()>3)
            convexityDefects(contours[i], inthull[i], defects[i]);
    }
    //find  hulland contour and defects end here
    //this will find largest contour
    for (int i = 0; i< contours.size(); i++) // iterate through each contour.
    {
        double a = contourArea(contours[i], false);  //  Find the area of contour
        if (a>largest_area)
        {
            largest_area = a;
            largest_contour_index = i;                //Store the index of largest contour
        }

    }
    //search for largest contour has end

    if (contours.size() > 0)
    {
        drawContours(original, contours, largest_contour_index, CV_RGB(0, 255, 0), 2, 8, hierarchy);
        //if want to show all contours use below one
        //drawContours(original,contours,-1, CV_RGB(0, 255, 0), 2, 8, hierarchy);
        if (showhull)
            drawContours(original, hull, largest_contour_index, CV_RGB(0, 0, 255), 2, 8, hierarchy);
        //if want to show all hull, use below one
        //drawContours(original,hull,-1, CV_RGB(0, 255, 0), 2, 8, hierarchy);
        if (showcondefects)
            condefects(defects[largest_contour_index], contours[largest_contour_index],original);
    }
}

void condefects(vector<Vec4i> convexityDefectsSet, vector<Point> mycontour, Mat &original)
{
    for (int cDefIt = 0; cDefIt < convexityDefectsSet.size(); cDefIt++) {

        int startIdx = convexityDefectsSet[cDefIt].val[0]; Point ptStart(mycontour[startIdx]);

        int endIdx = convexityDefectsSet[cDefIt].val[1]; Point ptEnd(mycontour[endIdx]);

        int farIdx = convexityDefectsSet[cDefIt].val[2]; Point ptFar(mycontour[farIdx]);

        double depth = static_cast<double>(convexityDefectsSet[cDefIt].val[3]) / 256;
        //cout << "depth" << depth << endl;
        //display start points
        circle(original,ptStart,5,CV_RGB(255,0,0),2,8);
        //display all end points
        circle(original, ptEnd, 5, CV_RGB(255, 255, 0), 2, 8);
        //display all far points
        circle(original,ptFar,5,CV_RGB(0,0,255),2,8);
    }

}
```

Using the code above in conjunction with some input key prompts from the user allowed me to view each of the different display methods individually and fine tune my HSV thresholds to minimize defects. Below is an example of the Base hand right through to the display of contour points.

{% capture fig_img %}
![Hand Base]({{ site.url }}/images/posts/2016-09-28/hand-detect-01.png)
![Hand with HSV calibration]({{ site.url }}/images/posts/2016-09-28/hand-detect-02.png)
![Hand with contour boundaries]({{ site.url }}/images/posts/2016-09-28/hand-detect-03.png)
![Convex hull and Contours]({{ site.url }}/images/posts/2016-09-28/hand-detect-04.png)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>Contour display process</figcaption>
</figure>

The final step was to add a convex hull around the outside of the hand based on the contour points on the finger tips.

{% capture fig_img %}
![Hand detect convex hull]({{ site.url }}/images/posts/2016-09-28/hand-detect-convex-hull.gif)
{% endcapture %}

<figure>
  {{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>Hand detect convex hull</figcaption>
</figure>

## Plan
---

The next step for me to take is to map a point on the hand that I will call the center. From that I can run lines out from the fingers and see if I can add a layer that represents the distance from the wrist to the center of the hand.

## References
---

How to find convexity defects and draw them - [https://www.youtube.com/watch?v=DEHk-5xbJhU](https://www.youtube.com/watch?v=DEHk-5xbJhU)

Hand Tracking And Recognition with OpenCV - [http://sa-cybernetics.github.io/blog/2013/08/12/hand-tracking-and-recognition-with-opencv/](http://sa-cybernetics.github.io/blog/2013/08/12/hand-tracking-and-recognition-with-opencv/)