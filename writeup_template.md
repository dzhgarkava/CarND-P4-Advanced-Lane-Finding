## Writeup Template

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

[image1]: ./examples/chess_distort_undistort.png "Undistorted"
[image2]: ./examples/test_distort_undistort.png "Road Transformed"
[image3]: ./examples/binary_combo_example.png "Binary Example"
[image4]: ./examples/warped_straight_lines.png "Warp Example"
[image5]: ./examples/color_fit_lines.png "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of the IPython notebook located in [Here](https://github.com/dzhgarkava/CarND-P4-Advanced-Lane-Finding/blob/master/CarND-P4-Advanced-Line-Finding.ipynb)  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect chessboard corners for 17 of 20 images.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will show how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image. Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image. I've added several functions: `abs_sobel_thresh()`, `mag_thresh()`, `dir_threshold()`, `combine_thresholds()`, `hls_select` for combining color and gradient thresholds. I call this functions in 5th code cell (lines 8-12). Here's an example of my output for this step. 

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warper()`, which appears in the 4rd code cell of the IPython notebook.  The `warp()` function takes as inputs an image (`image`). I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
        [[240,690],
        [564,470],
        [718,470],
        [1060,690]])
    
    dst = np.float32(
        [[240,720],
        [240,0],
        [1060,0],
        [1060,720]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 240, 690      | 240, 720      | 
| 564, 470      | 240, 0        |
| 718, 470      | 1060, 0       |
| 1060, 690     | 1060, 720     |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Code cell 8, lines 20-113. I've created a histogram of image, found peaks and by using sliding window identified pixels for left lane (red) and right lane (blue). After that I fit this pixels positions with a polynomial by usinf `np.polyfit()` method. Here's an example of my output for this step:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

Code cell 9, lines 3-18. I did calculation of the radius of curvature of the lane in local coordinates first and than converted these coordinates to world coordinates.

Code cell 9, lines 39-41. I calculated position of the car with respect to center of lane.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step Code cell 11 in the function `annotate_image()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output. Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_line.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail? What could you do to make it more robust?

Mostly the pipeline works well but it might fail in places with shadows on the road. We can improve it by tune the detection algorithm in case if next frame of video has very different places of left and right lanes.
