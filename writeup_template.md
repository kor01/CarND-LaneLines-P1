# **Finding Lane Lines on the Road**


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./misc_images/bad_case.png "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 10 steps:

1.  turn the image to grayscale

2. Gaussian blur the image to remove noise

3. detect the edges in the image using Cany edge detection

4. use polygon region to crop out the region of interest

5. use hough line extraction to extract lines from edge features

6. compute the slop of lines and cluster the lines by the sign of the slops

7. compute the average slop of each group (as smoothed slop)

8. compute a smoothed top point by average over line segment tops (threshold on y coordinate < 375)

9. use the smoothed top point and slop to infer the bottom point of each line

10. use draw_lines with thickness=5 and weighted_img to draw the extrapolated line on the image


In order to perform extrapolation, I implemented a new function `extrapolate_lane()` in `cell[6]: 40`

the function is called in process_image `cell[9]: 24`, after the hough line extraction


### 2. The Challenge Clip Algorithm Update:

1. the original pipeline does not work well for challenge clip, for reasons:

   a. the challenge video is in 720p resolution, does not match the 540p video in development

   b. the challenge clip has a lot of textures and lightning condition vibration

   c. the challenge clip has curved lane instead of straight one

2. my implementation in `optional_challenge.ipynb` address these problems by:

    a. normalize the clip to 540p to make all the pipeline parameter work as expected. `optional_challenge.ipynb cell[2]: 150`

    b. in slop clustering step, the positive group is taken as the lines with **slop > 0.45;** the negative group is taken as the lines with **slop < -0.45**; this update **effectively removed all the noise textures**. `optional_challenge.ipynb cell[2]: 100`

  3. these two updates fixed **most of the problems** in this challenge as illustrated in the video


### 2. Identify potential shortcomings with your current pipeline


  1. One potential shortcoming would be the algorithm pipeline is tailored to straight line detection and straight line motion, it will fail in turning motion.

  2. the algorithm is not robust w.r.t drastic lightning condition vibration; as illustrated by the bad cases in challenge clip, the left side is missing since the edge detector failed.

      ![bad case][image1]


### 3. Suggest possible improvements to your pipeline

  1. more robust edge detector that works in different lightning conditions (for example, HSV color scheme)

  2. more sophisticated geometric object extraction to accommodate the cases in curved lane

  3. single step sensor estimation can be improved by sequence state modeling; using **sequence model (bayes filter or variational ones https://arxiv.org/abs/1605.06432)** to filter the lane state could robustly remove the noise in one step sensor measurement
