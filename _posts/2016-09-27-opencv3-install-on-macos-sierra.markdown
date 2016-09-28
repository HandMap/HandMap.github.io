---
layout: post
title: "OpenCV 3.x install on macOS Sierra"
date: 2016-09-27
excerpt: I have fought the good fight and feel the need to share the process I followed to fix my issues
tags:
- opencv
- sierra
- java
- python
---

## Problem
---

So for the past month and a half I have battled furiously with getting OpenCV 3.x installed on my Macbook Pro that I accidentally upgraded to Sierra on.

```bash
#import <QTKit/QTKit.h>
        ^
1 error generated.
make[2]: *** [modules/videoio/CMakeFiles/opencv_videoio.dir/src/cap_qtkit.mm.o] Error 1
make[2]: *** Waiting for unfinished jobs....
make[1]: *** [modules/videoio/CMakeFiles/opencv_videoio.dir/all] Error 2
make: *** [all] Error 2
```

I'm not alone with troubles as it seems that some of the core video/image access and manipulation libraries are no longer bundled with Xcode 8.0 command line tools. To add insult to injury even if you install version >7.0 command line tools you'll still have issues on Sierra.

## Solution
---

So the solution has already been implemented for the OpenCV upstream HEAD branch however the changes haven't quite been merged with the standard repo that brew/science uses to install and build OpenCV. This can be fixed by installing OpenCV with the following arguments

```bash
brew tap homebrew/science
brew install opencv3 --HEAD --with-contrib --with-python3 --with-java
```

You can omit the `--with-python3` and `--with-java` off the end if you don't want it installed for Python3.x or java, however there's no harm having it built. You might just want to make sure you've got your Python path registered with Homebrew.

Below is a list of other arguements that are available if you see fit:

```bash
==> Options
--32-bit
	Build 32-bit only
--c++11
	Build using C++11 mode
--with-contrib
	Build "extra" contributed modules
--with-cuda
	Build with CUDA v7.0+ support
--with-examples
	Install C and python examples (sources)
--with-ffmpeg
	Build with ffmpeg support
--with-gphoto2
	Build with gphoto2 support
--with-gstreamer
	Build with gstreamer support
--with-jasper
	Build with jasper support
--with-java
	Build with Java support
--with-libdc1394
	Build with libdc1394 support
--with-opengl
	Build with OpenGL support (must use --with-qt5)
--with-openni
	Build with openni support
--with-openni2
	Build with openni2 support
--with-python3
	Build with python3 support
--with-qt
	Build the Qt4 backend to HighGUI
--with-qt5
	Build the Qt5 backend to HighGUI
--with-quicktime
	Use QuickTime for Video I/O instead of QTKit
--with-static
	Build static libraries
--with-tbb
	Enable parallel code in OpenCV using Intel TBB
--with-vtk
	Build with vtk support
--without-eigen
	Build without eigen support
--without-numpy
	Use a numpy you've installed yourself instead of a Homebrew-packaged numpy
--without-opencl
	Disable GPU code in OpenCV using OpenCL
--without-openexr
	Build without openexr support
--without-python
	Build without Python support
--without-test
	Build without accuracy & performance tests
--HEAD
	Install HEAD version
```

I would highly recommend installing and symlinking Python2.x and Python3.x with brew instead, as it'll keep things neat and tidy.

Once you've successfully installed OpenCV 3.x you'll needed to symlink a couple paths for your Python2.x and Python3.x libraries. This is done with the following commands

```bash
## Python 2.7
echo /usr/local/opt/opencv3/lib/python2.7/site-packages >> /usr/local/lib/python2.7/site-packages/opencv3.pth

## Python 3.5
echo /usr/local/opt/opencv3/lib/python3.5/site-packages >> /usr/local/lib/python3.5/site-packages/opencv3.pth
```

Your version of the command above might vary depending on where you local python libraries are installed. Amend as necessary.

And presto! you should be able to import and check the version of OpenCV from the python command line interpreter

```bash
# nathan at nathan-macbook in ~ [23:51:55]
→ python

Python 2.7.12 (default, Jul 31 2016, 15:05:20)
[GCC 4.2.1 Compatible Apple LLVM 8.0.0 (clang-800.0.33.1)] on darwin
Type "help", "copyright", "credits" or "license" for more information.

>>> import cv2
>>> cv2.__version__
'3.1.0-dev'
```

Java is fairly similar and the libraries you'll be working with can be found at the following location:

```bash
/usr/local/opt/opencv3/share/OpenCV/java/opencv-310.jar
```

You can include this library and utilize it in a similar fashion to python.

```java
import org.opencv.core.Core;

public class Main {

    public static void main(String[] args) {
        System.out.println(Core.VERSION);
    }
}
```

## Conclusion
---

I am so unbelievably happy I managed to get this working after a painful month or so working out of a virtual machine...