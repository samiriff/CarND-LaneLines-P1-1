# **Finding Lane Lines on the Road** 

## Writeup
---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. 
- First, I converted the images to grayscale
- Then I defined a kernel of size 3 for Gaussian smoothing / blurring
- This was followed by running Canny's edge detection algorithm with high and low thresholds determined using Otsu's method
- Then I applied the Hough transform on the resulting image with a distance resolution of 1 pixel; an angular resolution of 2 degrees; with 90 as the minimum number of votes; 3 as the minimum number of pixels making a up a line; and 3 as the maximum gap in pixels between connectable line segments.
- Finally, I created a weighted image from the output of the hough lines transform using the default parameters for α, β and γ.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by separating the line segments returned by the Hough transform into two categories:
- Lines that lie towards the left of the region of interest based on the fact that the slope of such lines is positive
- Lines that lie towards the right of the region of interest based on the fact that the slope of such lines is negative
In order to eliminate erroneous lines such as horizontal lines in the challenge video, I added an additional check to ensure that all lines that didn't lie within at most 10 degrees of the left or right edges of the region of interest were discarded.
The draw_lines() method was then called separately for each category of lines. Within the draw_lines() method, I fitted a polynomial using np.polyfit to get a line of the form y = mx + b. After ensuring that this line lay within at most 10 degrees of the left or right edges of the region of interest, I constructed a line which extended from ((y - b) / m, y) where y is the height of the image till ((y' - b) / m, y'), where y' is half the height of the image.

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when the road turns abruptly towards the right or left, since I am relying on the fact that the slopes of lines that lie to the left of the region of interest is positive and that of the lines that lie to the right of the region of interest is negative.

Another shortcoming could be that this pipeline relies on manually specifying a region of interest, which would vary based on the dimensions of the image (as seen with the challenge output). There could be potential issues while moving on a road with sharp turns or if the car suddenly moves up or down a steep slope. This pipeline also assumes that there won't be any other cars in front that cover one or both lane lines.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to consider the actual distance of lines from the edges of the region of interest instead of relying only on the slope. 

Another potential improvement could be to improve the output of the challenge video in which there are frames where the detected lane lines get very skewed during the turn. 
