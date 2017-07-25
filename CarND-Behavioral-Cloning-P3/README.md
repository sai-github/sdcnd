# **Behavioral Cloning Project** 

---
The goals / steps of this project are the following:
* Use the simulator to collect data of good driving behavior
* Build, a convolution neural network in Keras that predicts steering angles from images
* Train and validate the model with a training and validation set
* Test that the model successfully drives around track one without leaving the road


[//]: # "Image References"

[image1]: ./images/architecture.png "Architecture"
[image2]: ./images/dataset.png "Visualize data in sample set"
[image3]: ./images/gifrun.mp4 "Final video gif"

---
###Files Submitted & Code Quality

####1. Submission includes all required files and can be used to run the simulator in autonomous mode

My project includes the following files:
* model.py containing the script to create and train the model
* drive.py for driving the car in autonomous mode
* model.h5 containing a trained convolution neural network 
* README.md  summarizing the results

####2. Submission includes functional code
Using the Udacity provided simulator and my drive.py file, the car can be driven autonomously around the track by executing 
```sh
python drive.py model.h5
```

####3. Submission code is usable and readable

The model.py file contains the code for training and saving the convolution neural network. The file shows the pipeline I used for training and validating the model, and it contains comments to explain how the code works.

###Model Architecture and Training Strategy

####1. An appropriate model architecture has been employed

As the image has RGB data and we beforehand cannot judge which channel can provide useful data I am considering 3 1x1 filters. Next I am using 3 convolution layers and 3 Fully connected layers. Convolution layers help to filters things like lines, shapes and higher level features. I also tried using LeNet and architecture provided in Nvidia paper but this worked for me.

This architecture was mentioned a blog https://chatbotslife.com/using-augmentation-to-mimic-human-driving-496b569760a9

####2. Attempts to reduce overfitting in the model

I introduced dropouts and ELU after convolutions. I had a issue with keras version on my system and AWS which prevented me from using weight regularizers. 

####3. Model parameter tuning

The model used an adam optimizer, so the learning rate was not tuned manually. It was suggested in cs231n course and other places that using adam benfits from manual tuning of learning rate for basic models.

####4. Appropriate training data

Training data was chosen to keep the vehicle driving on the road. I used a combination of center lane driving, recovering from the left and right sides of the road. I also augmented images with different brightness values.3

###Model Architecture and Training Strategy

####1. Solution Design Approach

As Convnets have better performance when dealing with images I tried LeNet, Nvidia.

In order to gauge how well the model was working, I split my image and steering angle data into a training and validation set. After running for 5 epochs I found MSE for test data surfacing and training data still decresing. I stopped after 2 epochs to prevent overfitting.

The final step was to run the simulator to see how well the car was driving around track one. There were a few spots where the vehicle fell off the track. I was training my model on cropped image but in autonomous mode I fed complete image to predict. This caused me a lot of time to debug the problem. After adjusting `image_array` in `drive.py` the car was able to drive autonomously on the track

####2. Final Model Architecture

![alt text][image1]

####3. Creation of the Training Set & Training Process

I drove 4 to 6 laps in splits with different driving conditions. In one lap I would maintain 30mph and in others I would brake while taking a turn. Although throttle and brake were not used in this project, I hope even these can be predicted for some other use cases. 

![alt text][image2]

I used generators with batch size of `256` and by using steering correction angle I could generate `training :  (46242,) validation :  (5139,)` samples

![track1](/images/gifrun.mp4)
