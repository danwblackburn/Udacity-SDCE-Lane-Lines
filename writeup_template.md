# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I did a standard Guassian blur with a kernel size of 5. I applied a canny filter to detect edges using thresholds of 50 and 150. These were the same values used in our course, but they worked well. Then a mask was applied to the image to limit the scope to just the bottom portion of the image. The mask is applied based on set pixel locations so the size of the image must be standard. Then I applied a hough transformation to determine where the edges are. Originally, the lanes were simply drawn in the exact locations of the detected hough lines using cv2.line() , but was eventually changed to meet the requirements of only a single lane line on the left and right.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by sorting the individual lanes lines in to two arrays of negative or positive slopes. I used an extrapolate function whiched determined the "average line" on each side and then extrapolated that line to the top and bottom of the mask. The average line was calculated by simply averaging the x and y coordinates of the top and bottom points of each line.


### 2. Identify potential shortcomings with your current pipeline

One potential shortcoming is that the images that are fed in to the pipeline must all be a standard size in order for the mask to work correctly.

Another shortcoming is the draw lines/extrapolation code. The algorithim simply takes an average of all lines deteced by the hough transform so it is very susceptible to noise from random lines that are detected, but not actually a part of the lanes. This has the effect of the lane lines randomly jumping around for a single frame or so when the transformation detects a random line.

The shape of the mask also limits the pipeline to mostly detecting lanes that run straight in front of the vehicle. It is not as effective on lanes that curve to the side and exit the mask.


### 3. Suggest possible improvements to your pipeline

The standard size is a relatively easy fix. I would just need to add a resize component to the pipeline using CV2. Or define the size of the mask based on the size of the image.

The extrapolation code could be fixed by remembering an average of past lanes. Because lanes are mostly continous on the road you could average the current frame's lines with say a second or so of past lines and that would prevent most of the noise and jumping.

A color filter could also be used to help prevent the detection of lines that are not lane markers since all lane markers come in a few standard colors.

The detection of past lane lines could also be used to determine the shape of the mask for the current shape. The mask would be set as an area around past lanes, but a little larger to allow the detection of new lines. This would make the mask more accurate and allow for the mask to change to the shape of curved roads.
