## Advanced Lane Finding Project

### This project suggests Computer Vision techniques for identifying lane markings and estimation the radius of curvature of lane and position of vehicle on lane.

---

**Advanced Lane Finding using Computer Vision**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./myoutputs/distortion_correction.png "Camera Correction"
[image3]: ./myoutputs/bird_view.png "Bird View"
[image4]: ./myoutputs/rgb_hls.png "RGB and HLS Channels"
[image5]: ./myoutputs/combined.png "RGB HLS combined"
[image6]: ./myoutputs/histogram.png "Histogram for identifying Starting point of lane"
[image7]: ./myoutputs/blind_search.png "Sliding Window method"
[image8]: ./myoutputs/polynomial_fit.png "Fitting 2nd degree polynomial"
[image9]: ./myoutputs/final.png "Before and Final Images"
[video1]: ./project_video_output.mp4 "Video"

## [Rubric Points](https://review.udacity.com/#!/rubrics/571/view)

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---


### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the function `calibrate_camera`.  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single image)

#### 1. Distortion-corrected image.

To rectify distortion due to camera lens in the image `cv2.calibrateCamera` returns *Camera Matrix, Distortion Coefficients, etc*. This provides correction near corners and helps in predicting lane curvature with less error. 
![alt text][image2]


#### 2. Perspective Transform

The code for my perspective transform includes a function called `warp()`.  The `warp()` function takes as inputs an image (`img`), as well as camera matrix (`mtx`) and distortion coefficients (`dist`) points.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 460      | 320, 0        | 
| 203, 720      | 320, 720      |
| 1127, 720     | 960, 720      |
| 695, 460      | 960, 0        |


![alt text][image3]

#### 3. Threshold for Color Transform and Gradient in generating Binary image.

Different feaures are picked up by different filters. Some real cases involve different road intensities, lane colors, shadow effect of surrounding objects like tree etc. Binary images obtained using processing of RGB channel and HLS channel are combined to identify overall features.

RGB steps:
* Sobel Operator along x
* Magnitude of Sobel
* Direction of Sobel

HLS steps:
* Extract S- Channel
* Apply threshold

![alt text][image4]

By combining both images we get identify lane markings with greater confidence.

![alt text][image5]

#### 4. Blind Search for Lane markings

I first take a histogram along all the columns in the lower half of the image.

![alt text][image6]

With this histogram I am adding up the pixel values along each column in the image. In my thresholded binary image, pixels are either 0 or 1, so the two most prominent peaks in this histogram will be good indicators of the x-position of the base of the lane lines. I can use that as a starting point for where to search for the lines. From that point, I can use a sliding window, placed around the line centers, to find and follow the lines up to the top of the frame.

![alt text][image7]

#### 5. Radius of Curvature Calculation

From identified lane markings I tried fitting a second order polynomial. After polynomial fit I tried converting it from pixel coordinates to meters coordinates. Then I calculated radius from basic differential equations and algebra as following snnipet

![alt text][image8]

```python
   left_curverad = ((1 + (2*left_fit[0]*y_eval + left_fit[1])**2)**1.5) / np.absolute(2*left_fit[0])
   right_curverad = ((1 + (2*right_fit[0]*y_eval + right_fit[1])**2)**1.5) / np.absolute(2*right_fit[0])
```


#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

For video processing I implemented everything apart from camera calibration in following function as `video_process` which takes in `img`, `camera_matrix` and `distortion_coefficients`. After identifying lane lines, markings are made onto the image and returned as unwarpped image with lane markings

![alt text][image9]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

It seems ok on test video but would fail in extreme conditions. On challenge videos it behaves abnormally. It might be because the is not so reliable when there is huge change in road intensity and shadows. As the lane path is a bit long it fails if there is some obstacle or vehicle infront of it.

Using some frames data in calculating radius curvature might reduce sudden change in curvature. Using previous frames information might also help in reducing error while finding lane.  
