## Advanced Lane Finding Project

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use sobel x and s channel filtering to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/1.png "Camera calibration"
[image8]: ./output_images/8.png "Distortion correction"
[image2]: ./output_images/2.png "Threshold"
[image3]: ./output_images/3.png "Perspective correction"
[image4]: ./output_images/4.png "Warp Example"
[image5]: ./output_images/5.png "Histogram lane detection"
[image6]: ./output_images/6.png "Search window around detected line"
[image7]: ./output_images/7.png "Draw lane on road"
[video1]: ./output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Writeup / README

All code is in the IPython notebook at "./Advanced Lane Detection.ipynb"

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the 4th and 5th cells titled "Utility functions for camera calibration" and "Extract object points and image points for camera calibration".

`calibrate_camera()` takes in a list of calibration image filenames and then runs `find_chessboard_corners()` on each image. It returns a subset of the camera calibration coefficients returned from `cv2.calibrateCamera`.

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

I applied the calibration coefficients to the test image below.

![alt text][image8]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

The code for the transforms is in cell 6 and 7 under the sections titled "Utility functions for filtering an image" and "Test combined S channel and Sobel filter". I used a Sobel x gradient combined with an S channel filter to create the binary image. For the Sobel gradient, I converted the image to grayscale and then used `cv2.Sobel`. For the S channel filter, I converted the image to HLS and then dropped the other two channels.

![alt text][image2]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code is in cells 78 and 79 under the sections titled "Utility functions for perspective transform" and "Test perspective warp". I manually created a source and destination transformation in the function `src_dst()`, with the shape parameterized so that I could more easily tweak it when testing. `warp()` converts the image to a bird's eye view, and `unwarp` does the inverse.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The code is in cells 81, 82, 83 under the sections titled "Utility functions for histogram with sliding window", "Utility functions for window search around prior line", and "Test histogram". I tried out both using a histogram and a convolution, but ultimately decided on using the histogram to detect lane lines because it was easier for me to visualize and debug. `find_lane_pixels()` implements the histogram, though it is only run once to detect the initial lane. 

![alt text][image5]

Subsequent frames use `search_around_poly()`, which uses the best fit lines from previous frames to use a targeted "search" area.

![alt text][image6]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The code is in cells 133 under the section "Test curvature". `measure_curvature_real()` uses the formula for radius of curvature and scales y using `ym_per_pix` to determine the result in meters. `measure_lane_offset()` calculates the left and right x values at the bottom of the image, averages them, and then subtracts half of the width of the frame to determine the offset, which is then multiplied by `xm_per_pix` to determine the offset in meters.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The code is in cell 141, with the function name `process_image()`. I used this function to test the pipeline against test images and also for creating the video output.

![alt text][image7]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?
