
**Finding Lane Lines on the Road** 
---

The goals / steps of this project are the following:
\
* Make a pipeline that finds lane lines on the road
\
* Reflect on my work


[//]: # (Image References)

[image1]: ./test_images_output/solidYellowCurve2.jpg
![After processing the image][image1]

---

### Reflection

#### 1. Pipeline discription

Initially the color image is converted into grayscale image. Then a Gaussian Noise Kernal is applied 
on the grayscale image to decrease gradient in color so as to remove unwanted edge detection for later 
stages. Next, Canny edge detection is used. As our goal is to find lane markings next we try to mask
unwanted regions my defining a polygon. This polgon is now our area of interest and edges detected outside 
of this region are ignored as of now.\
\
Now I tweaked for appropriate values. Our tuning of parameter is critical in noticing markings of different color. Now, the masked canny image is fed into hough transform and corresponding lines are drawn on our copy 
of orginal image.\


#### Changes made in `draw_lines` function

From `numpy array` of `lines` the slope is calculated for x1,y1,x2,y2. Depending on the slope,
if it is positive or negative it is classified as left or right lane marking. To avoid any unwanted lines
the or lines that are misleading the `numpy array` of `left_slope` and `right_slope` are sorted and the median
of the `array` is used in extrapolation.\
As we are always sure the lane markings start from the bottom of the screen the points from `lines` and the median of `slopes` corresponding to left and right are used to draw lane lines. In some cases if the lines are 
not identified care is taking by not drawing any line for that image.





#### 2. Identify potential shortcomings with your current pipeline

As we are drawing maximum of 2 lines with extrapolation identification of cured lanes is not possible.
Due to inefficient parameter tuning identifying lanes with different colors is introducing noise.


#### 3. Suggest possible improvements to your pipeline

Possible lane marking as identified from hough transform and some sort of curve fitting can be done for identifying lanes. Memory of lane marking from previous images can be used while processing next image as
we are sure from before image that there was a line marking at that location.
