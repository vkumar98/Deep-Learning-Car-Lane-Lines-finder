**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier.
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: For those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/car_not_car.png
[image2]: ./output_images/HOG_image.png
[image3]: ./output_images/sliding_windows.png
[image4]: ./output_images/heatmap.png
[image5]: ./output_images/output.png
[video1]: ./project_video.mp4


###Histogram of Oriented Gradients (HOG)

Here are the steps I used to extract HOG features from the training images set.

    Step1 - Read all the vehicle and non-vehicle image locations into list.
            
    Step2 - Create a function extract_features that accepts following parameters
            imgs - List of images
            cspace - Color Space. For this project: HLS
            orient - Number of bins: 9 
            pix_per_cell- Pixels per cell: 8   
            cell_per_block- Cells per block: 2
            hog_channel- Hog Channel: ALL

            Return features
    
            Within this function I normalized the image using StandardScaler().fit(). I tried with different values for parameters and
            above values gave me a classifier accuracy of 0.9978

   
    Example of car and non-car image
![alt text][image1]

    
    Eample of car and it's HOG image and code for this is in under display HOG feature.

![alt text][image2]

####2. Explain how you settled on your final choice of HOG parameters.

 Used sklearn's train_test_split function to create train and test features(80/20 rule) from car/non-car image set. I tested model accuracy with various parameter combinations for extract_feature fuction and keep testing the model accuracy(linear SNM classifier). 

###Sliding Window Search
This was the most time consuming part of the project. I decided to use only lower half portion portion of image for sliding window search: ([600,1280],[400,720]). Tried combinations of window size of 128x128,64x64, 32x32 etc...and some combinations didn't work or were very slow. After many attempts, I finalized with following :

    for image location [400,720],[600,1280]    use window size: 128x128, overlap=0.8,0.8
    for image location [400,720], [600,1280]  use window size: 96x96,overlap=0.5,0.5
   
    I tested above sliding window settings on test images in test_images folder. Here is an example image with sliding window.

![alt text][image3]

There were many false positives along the left side of road and also at the bottom of the image. To remove these false positives I used heatmap and applied threshold <=2. Drew the boxes around label area and detected using scipy.ndimage.measurements.labels. Here is an example of image with heatmap and boxes.

![alt text][image4]

    Finally, DrawBox function is to test pipeline. This part is in under Test pipeline section. Initially it was very slow, and after testing with different sliding windows size it started performing better.


### Video Implementation

Here's a link to my video result
![alt text][video1]


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

    While testing with test images there were some false positives showing across the road then I created a heatmap and applied threshold value >= 2. Scipy library label returns blob around the heatmap and construct box(min(x,y)- max(x,y)) around the blob.

###Discussion
  The problem i see with my pipeline is the number of windows I am using for searching, it is slowing down vehicle in video search as number of windows increase. When I use few windows then it's not detecting vehicles all the time. To speed up window search I just focused on right hand side of the road. This pipeline will not work if there are vehicle on the left side or my car is in right lane/Center Lane. Not sure how it will react if there are any pedestrians on the road, may be need more training data. 



