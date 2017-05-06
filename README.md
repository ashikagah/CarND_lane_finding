### Self-Driving Car Engineer, Udacity ###
# Project 1: Finding Lane Lines #
Hiroshi Ashikaga, MD, PhD, Baltimore, Maryland, USA

## 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function. ##

My pipeline consists of 7 steps:
### 1) Load image ###

• I used the function mpimg.imread()

### 2) Apply grayscale ###

• I used the function grayscale(), which is the same as cv2.cvtColor()

### 3) Apply Gaussian smoothing ###

• I tried several different kernel size and found that the kernel size of 9 provides a happy medium between signal and noise.

### 4) Apply Canny transform ###

• I used the function canny, which is the same as cv2.Canny()

• I didn’t change the high (130) or the low threshold values (50).

### 5) Create left and right regions of interest (ROI) ###

• I split the ROI in two parts, right and left, so I can define one straight line for each ROI

### 6) Apply Hough transform ###

• I applied Hough transform to each ROI with the same parameters

• I changed the min_line_length (60) and max_line_gap (200).

### 7) Draw the lines of the original image ###

• I combined the left and the right ROIs in the weighted_img() function to draw two lines.

### How I modified the draw_lines() function: ###

First, I calculated the mean coordinates of line edge positions in each of left and right ROI. I reshaped the lines, which is the output matrix of cv2.HoughLinesP() function, so I can calculate the mean coordinates of two edges of lines (x1, y1, x2, y2). Second, I calculated the median of all the slopes in each ROI. I am assuming that the median slope of all the line segments would most closely represent the slope of the lane line. Finally I draw the line with the median slope that goes through the mean coordinates.

## 2. Identify potential shortcomings with your current pipeline ##

I feel that the result on all the test images was reasonable. The application of this pipeline to solidWhiteRight.mp4 was OK, but an obvious issue is the lack of smoothness between frames. For example, the lane lines appear “shaky” over the entire duration of the movie.
In terms of my modification to the draw_lines() function: Initially I started by using the mean slope, but the representation was poor, likely because all the slope, good or bad, contribute equally to the mean slope. However, I don’t think using the median slope is the best way. I don’t think the sample size (number of line segments is only a few dozens at most) is not large enough to take advantage of the median. This is obvious from the fact that my pipeline performed miserably on the challenge movie.

## 3. Suggest possible improvements to your pipeline ##

• One potentially effective way to estimate the slope of the lane line is to use a weighted average of the slope by the length of the line segments, but I didn’t have time to implement it in the pipeline.

• To improve the lack of smoothness between movie frames, a reasonable approach is to implement smoothing the line edge coordinates between frames. For example, instead of estimating the lane lines at each movie frame, one can calculate the line edge coordinates as the moving average of 5-7 frames in the time direction. This is a method commonly used in medical imaging (CT, MRI) to quantify the heart motion and catheter motion in the heart using magnetic field sensors. The good news is that the motion of the object (e.g. lane lines) between the movie frames becomes smooth. However, the bad news is that you are essentially reducing the temporal resolution of the data acquisition. In case of the self-driving car, the reduction of temporal resolution by smoothing over time is likely permissible below the upper bound that is determined by the safety data based on human physiology.
