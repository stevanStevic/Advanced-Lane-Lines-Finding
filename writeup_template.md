## Advanced Lane Line Findings

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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

[//]: # (Image References)

[image1]: ./examples/camera_calibration.png "Undistorted"
[image2]: ./examples/undistored_img.png "Road Transformed"
[image7]: ./examples/sobel.png "Sobel gradients"
[image8]: ./examples/s_threshold.png "S channel masking"
[image9]: ./examples/combined_threshold.png "Combined threshold"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the IPython notebook located in **"./camera_calibration.ipynb"**.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![Undistorted image after camera calibration][image1]

### Pipeline (single images)

Implementation of pipeline can be found in **Advanced_Lane_Line_Finding.ipynb** notebook. In descritpion of pipeline steps I will reference cells from this notebook.

#### 1. Distortion correction

Distortion correction that was calculated via camera calibration has to be correctly applied to each image. It uses `cv2.undistort()` with loaded camera matrix and distortion coeffs parameters. This can be seen in 

![Road image undistorted][image2]

#### 2. Color and gradient threshold

We can combine two types off thresholds: color and gradient. 
Gradient threshold can be obtained by running Sobel kernel over the image and finding gradients. There are 3 types of gradients that we explored: individual X and Y, magnitude and direction of the gradient. Result can be seen in image below.

![alt text][image7]

I've also applied binary mask on S channel after converting the image from RGB to HSL color space. I've used S channel since it works well on both yellow and white lines. On a image below is result of only s channel extracted and after applying threhsolds.

![alt text][image8]

I used a combination of color and gradient thresholds to generate a binary image seen below. I used following masking: `s_channel | x_sobel | (magnitude & direction)`.

![alt text][image9]

#### 3. Perspective transform.

The code for my perspective transform includes a function called `perspective_transform()`, which appears in the 3rd code cell of the IPython notebook.  The `perspective_transform()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
cols, rows = combined.shape[::-1]
top_limit = rows * 0.63
down_limit = rows - 20
src_point1 = [280, down_limit]
src_point2 = [595, top_limit]
src_point3 = [725, top_limit]
src_point4 = [1125, down_limit]
src = np.float32([src_point1, src_point2, src_point3, src_point4])

dst_point1 = [250, rows]
dst_point2 = [250, 0]
dst_point3 = [1200, 0]
dst_point4 = [1000, rows]
dst = np.float32([dst_point1, dst_point2, dst_point3, dst_point4])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| , 460      | 320, 0        | 
| 203, 720      | 320, 720      |
| 1127, 720     | 960, 720      |
| 695, 460      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in `my_other_file.py`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
