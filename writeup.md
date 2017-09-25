## **Advanced Lane Finding Project**

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

[image1]: ./output_images/origin_undistorted_grids.png "Undistorted Grids"
[image2]: ./output_images/origin_undistorted.png "Undistorted"
[image3]: ./output_images/src_reigen.png "Corners"
[image4]: ./output_images/warped.png "Warp Example"
[image5]: ./output_images/combined_binary.png "Binary Image"
[image6]: ./output_images/base_position.png "Laen line base"
[image7]: ./output_images/polyfit_left_right.png "Polyfit"
[image8]: ./output_images/draw_lane_area.png "Project to Ori"

### Camera Calibration

The code for this step is contained in the IPython notebook  "camera_calibration.ipynb".  

First I define the chessboard size ,which is 9x6. Then I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  

I use `cv2.findChessboardCorners` to find `imgpoints` in the images from 'camera_cal/calibration*.jpg'.  The (x, y) pixel position of each of the corners in the image plane will be found, if it has successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained below result.

I save the camera matrix and distortion coefficients to `calibration_wide/wide_dist_pickle.p`

![alt text][image1]

### Pipeline

#### 1. Load clibration parameters and distortion-corrected image.

Load clibration parameters from `calibration_wide/wide_dist_pickle.p` and use `cv2.undistort` to undistort image.
below is the image, which I apply the distortion correction:
![alt text][image2]

#### 2. perspective transfrom

I use `cv2.getPerspectiveTransform` to generate perspective transfrom matrix. I choosing four source points manually in original image as Below.
![alt text][image3]

Then, Warp the image using the perspective transform.
![alt text][image4]


#### 3. binary image

I convert the image to HLS color. Then used a combination of color(S channel) and gradient thresholds(L channel) to generate a binary image (steps at cell 23 function **pipline()** in `work.ipynb`).  Here's an example of my output for this step.  (note: this is not actually from one of the test images)
For the threshosds, I use interact slide bar to find the best min,max value.

![alt text][image5]


#### 4. Lane lines polynomial

1) First I use histogram of the lower part of the binary image to find the base left,right lane start position. Code is in cell 27.
 In below image, we can see the left lane bottom x = 370, right lane bottom x = 1100.

![alt text][image6]

I slide the image to 9 block(horizontally). for each slice , I apply the histogram as above, and find lane line up to the top of the frame.

![alt text][image7]

#### 5. radius of curvature

I did this in in cell 36 of `work.ipynb`. I use the y value corresponding to the bottom of the image.
```python
curvature = ((1 + (2 * line_fit[0] * y_eval + line_fit[1]) ** 2) ** 1.5) \
                             / np.absolute(2 * line_fit[0])
```

#### 6. porject back

I implemented this step in cell 37 `draw_lane_area()` in `work.ipynb` Here is an example of my result on a test image:

![alt text][image8]

---

### Pipeline (video)

Here's a [link to my video resul](https://youtu.be/4D2qxNSRwCo)

the video is in outpu_videos/project_video.mp4

---

### Discussion

#### 1. It is difficult to manually select the source , destinate points , which used to calculate perspective transform matrix.

#### 2. the thresholds ,filter binary image, didnot work well , when the road surface color changed.

#### 3. curvature is not accurate.
