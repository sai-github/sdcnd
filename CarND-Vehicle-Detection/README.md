### Vehicle Detection Project

---

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Estimate a bounding box for vehicles detected by constructing a pipeline of above steps to process a video stream.

[//]: # "Image References"
[image1]: ./examples/car_not_car.png
[image2]: ./examples/HOG_example.jpg
[image3]: ./writeup_images/color_channel.png
[image4]: ./writeup_images/test1.png
[image5]: ./writeup_images/test2.png
[image6]: ./writeup_images/test3.png
[image7]: ./writeup_images/test4.png
[image8]: ./writeup_images/test5.png
[image9]: ./writeup_images/test6.png
[image10]: ./writeup_images/example_img.png
[video1]: ./project_video_output_full2.mp4

---
### README

### Histogram of Oriented Gradients (HOG)

#### 1. Extracting HOG features

The code for this step is contained with the function name `get_hog_features`

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`) using `hog()` from `skimage.features`. 

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

#### 2. Choice of HOG parameters.

I tried various combinations of parameters and found that using hog feature increases vector length thus increasing training time. Considering just 1 or 2 channels is not of much use as considering all of them increases testing accuracy significantly. If we have enough computing power considering all HOG channels is a better process. I was using AWS g2.2x system during working with project. Without considering any of the HOG channel worked decent enough on my system ( testing accuracy is around 91% )
![alt text][image3]

#### 3. Training the classifier

I trained using `LinearSVC()` from `sklearn.svm` in project. I tried to fit the training data using `LinearSVC().fit(X_train, y_train)` and then calculated testing accuracy and prediction using `LinearSVC().score(X_test, y_test`) and `LinearSVC().predict()`

### Sliding Window Search

#### 1. Sliding Window Implementation 

We can do some kind of masking in image and we wouldn't be searching for car on tree tops and sky. We can also implement pyramid kind of structure, smaller window size on top and bigger window size at botton because of the prespective aspect. 

#### 2. Examples of test images.

Ultimately I searched on three scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images.

![alt text][image10]

---

### Video Implementation

#### 1. There is no false positives in the video ( as much I remember :P )
Here's a [link to my video result](./project_video_output_full2.mp4)


#### 2. Combining overlaping bounding boxes

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  I am using prevoius `8` frames data using `deque`.

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are images using the pipeline
We have windows and their arrangement, next are the detections in these windows.
Then we apply heatmap to eleimate false positives and final is Car positions.

![alt text][image4]
![alt text][image5]
![alt text][image6]
![alt text][image7]
![alt text][image8]
![alt text][image9]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

As identifying images on the road is the end goal of project this pipeline just tracks images of car in the video. It would great to detect centroids on boxes and use that information for various things like providing memory to pipeline in tracking a particular car during it sight of view. We can also estimate the speed of car that might be useful for any other systems to make the car drive on it's own.

