# **Finding Lane Lines on the Road** 
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

<img src="examples/laneLines_thirdPass.jpg" width="480" alt="Combined Image" />

Overview
---

When we drive, we use our eyes to decide where to go.  The lines on the road that show us where the lanes are act as our constant reference for where to steer the vehicle.  Naturally, one of the first things we would like to do in developing a self-driving car is to automatically detect lane lines using an algorithm.

In this project you will detect lane lines in images using Python and OpenCV.  OpenCV means "Open-Source Computer Vision", which is a package that has many useful tools for analyzing images.  

To complete the project, two files will be submitted: a file containing project code and a file containing a brief write up explaining your solution. We have included template files to be used both for the [code](https://github.com/udacity/CarND-LaneLines-P1/blob/master/P1.ipynb) and the [writeup](https://github.com/udacity/CarND-LaneLines-P1/blob/master/writeup_template.md).The code file is called P1.ipynb and the writeup template is writeup_template.md 

To meet specifications in the project, take a look at the requirements in the [project rubric](https://review.udacity.com/#!/rubrics/322/view)


Writeup
---

### 1. The pipeline
There are following steps in the pipeline:
#### **Step 1**
Transform image to grayscale. Keep it grayed.
#### **Step 2**
Blur image, that is already grayed.
To blur the image, Gaussian blur with kernel size 5x5 px.
#### **Step 3**
Find all edges with Canny algorithm
Canny findes edges with the treshold between 50 (low) and 150 (high).
#### **Step 4**
Crop region of interest, where there lines may\shall appear
The region we want to crop is a polygon with following shape:
```
     ...........................................
     .                                         .
     .                 (0.593)                 .
     .      (0.47)_________________(0.52)      .
     .           |                 \           .
     .          |                   \          .
     .         |                     \         . 
     . (0.19) ------------------------- (0.8)  .
     .                 (0.84)                  .
     ...........................................
```
Where the numbers in bracket is a pixel coefficient vs image width\height
Just an example from above.
If image has height of 1000 px, the upper line starts from pixel 593 and the bottom line ends on pixel 840.
The gap over the region is necessary, because of the it usually contains sky, buildings etc.
The gap below the region usually contains car's hood.
#### **Step 5**
Apply Hough Line Tranform to find lines on an image
OpenCV's implementation of Hough Line is used to find lines on an image.
Params used are:
*rho* = 1
*theta* = pi/180
*threshold* = 1
*min line's length* = 5px
*max gap between lines* = 1px
Hough Line transform returns list of lines (it's start and end points).
#### **Step 6**
Extrapolate the lines and draw them
To extrapolate the lines first lines are separated to those being part of left line and being part of right line.
To separate them slope (m) and intercept (b) is calculated.
For each line, it's x coordinate is compared with "region of interest" upper points' X's and its slope is verified to check, if line part belows to left line or to right line.
When all lines are separated to "left line" and to "right line", each of them separately is extrapolated.
To conduct extrapolation OpenCV.fitLine method is used.
These 2 lines are then drawn from bottom of the screen to the highest points inside "region of interest".

### 2. Shortcomings
There are issues with lines in shadow, which are seen on the video "challenge.mp4".
Another issue is, that sometimes yellow line is also not ideally seen in grayscale.
And definitelly the method of defining "region of interest" could be better as well as extrapolating the lines (left & right).
Sometimes there are little lines next to original lines on the road. They are "found" by Hough Transform and they influence the "extrapolated" line.


### 3. Possible improvements
I think there is an improvement in calculating grayscale necessary. E.g. for each channels separately. I'll try this, however I don't manage it before P1 deadline. ;-)
I would try to implement more "dynamic" method of finding region of interest. The current one is static and always in the same place.
I would need to play more with all parameters (for Canny, Hough Tranform, etc.) and find the best ones in "automated way".



