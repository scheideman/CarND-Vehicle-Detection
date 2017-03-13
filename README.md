# Vehicle Detection
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

[//]: # (Image References)
[image1]: ./examples/scale1.5.jpg
[image2]: ./examples/scale2.jpg
---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

---
##Writeup / README:

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

- In the Detector class in detector.py the is a function `train` that calls the `extract_features` function from features.py
- The `extract_features` function uses the skimage function `hog` to extract hog feature vectors from all the images at the file path passed in to the imgs parameter


####2. Explain how you settled on your final choice of HOG parameters.

- After testing I settled on using all the channels from the image in YCrCb colorspace by concatenating the hog features extracted from each channel
- Also after testing I found that using 18 orientations, 8 pixels per cell and 2 cells per block gave me the best results 

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

- In the Detector class in detector.py the is a function `train` is used for training a linear SVM
- The features used are a concatenation of the hog features described above, with the color histogram (48 bins) and the spatial color features (32X32)
- Featurs were normalized using the sklearn `StandardScalar` class
- 20% of the training images were used for validation and were split using the sklearn `train_test_split` function

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

- I used the code provided in the lectures for the sliding window search. The `find_cars` function in the Detector class in detector.py shows this implementation. 
- For overlap I went with 75% overlap (2 cells per step), as this gave me the the best results. Anything lower did not give very reliable results.
- For scales I ended up using two (1.5,2) in the `multi_scale_detection` function in detector.py. This combination allows recognizes all the car close to the camera, and will sometimes pick up cars way ahead. 

####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

- I used the YCrCb colorspace and used a combination of HOG, color histogram and color spatial information for features. I also normalized the features. Below are some example detections
![alt text][image1]
![alt text][image2]
---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are six frames and their corresponding heatmaps:

![alt text][image5]

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from all six frames:
![alt text][image6]

### Here the resulting bounding boxes are drawn onto the last frame in the series:
![alt text][image7]



---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  


Here are links to the labeled data for [vehicle](https://s3.amazonaws.com/udacity-sdc/Vehicle_Tracking/vehicles.zip) and [non-vehicle](https://s3.amazonaws.com/udacity-sdc/Vehicle_Tracking/non-vehicles.zip) examples to train your classifier.  These example images come from a combination of the [GTI vehicle image database](http://www.gti.ssr.upm.es/data/Vehicle_database.html), the [KITTI vision benchmark suite](http://www.cvlibs.net/datasets/kitti/), and examples extracted from the project video itself.   You are welcome and encouraged to take advantage of the recently released [Udacity labeled dataset](https://github.com/udacity/self-driving-car/tree/master/annotations) to augment your training data.  


