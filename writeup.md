## Writeup - Advanced Lane Lines
---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.


## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it! All code is in the organized [project notebook](./project.ipynb). Images and explanations are provided in the notebook. An html rendering is provided for viewing the notebook.

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

Code for this step is under point #1 in the notebook:

Like we were taught in the lesson, I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function, as shown in the notebook.

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

See notebook step 2.

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

Code is under point #3, and examples are right below in the notebook.

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

I took an example image and examined it in Preview (native Mac photo-viewing app), and found four src points to transform. 
There are two methods: the first calculates the transform and returns the matrix, and the second applies the transformations to an image.

Code and examples can be found under point 4.

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Code and examples under point 5; there are two methods. The first calculates the poly-fit from scratch with sliding windows; however, this can be easily optimized to use a previously known confident polyfit and search around the area for lane line pixels. If the previous method is unable to find many pixels (low confidence), it defaults back to a full sliding window search until confidence is high again.

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

Step #6 shows code and examples.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

Step #7 shows code and examples of drawing in the filled lane and warping it back, as well as lane information calculations.

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

At the end, the full pipeline is defined and wrapped into functions and a Lane class is created to track fits and for smoothing.

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The two big issues are:

1. The predefined src-dst transformation points are incredibly camera- and image-shape specific. Also, the distance of the src rectangle means that the road has to be fairly straight; sharp turns like those in the "harder-challenge-video" will render the src rect useless, because the src rect won't actually end up being a rectangle in the real world. One option is to use a shorter src rectangle from the bottom, and then not transform it to the full height in order to keep information about the rest of the lane, although this will prove hard to tune and make accurate.

2. This again relates to the transformation points, but on the "easier" challenge video, my lane line detector gets confused between the divider and the yellow line being the left side of the lane, rendering the valid lane on the divider itself! (Not sure if driving on top of the divider would be safe :\). A more robust method is needed to filter out straight lines on the road that aren't actual lane lines. Perhaps a weight should be added towards lane lines that find themselves a certain distance apart (since the lane line width is previously known by the US regulations and such).

I'm excited and anxious to move forward into the Localization part of this Nanodegree in term 2, but I would like to come back and attempt to make this project more robust, as finding lane lines is a very important part of the self-driving car pipeline.
