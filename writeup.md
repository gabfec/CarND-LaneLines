## **Finding Lane Lines on the Road**
---
### Description


The line finding pipeline consists of the following steps:
  - convert the image to grayscale
  - configure the gaussian blur kernel size parameter (for the next step)
  - use a Canny edge detector to identify the edges
  -  define a region of interest and mask out what's outside of it
  - run a Hough transform to identify the lane lines
  - apply the lane lines on the original image


The helper functions were very useful, but I had to wrap them in a class so I can expose a more descriptive API, like this:

<code>
pipe = Pipeline(image)
img = pipe\
    .grayscale()\
    .gaussian_blur(5)\
    .canny(low_threshold=50, high_threshold=100)\
    .region_of_interest(vertices)\
    .hough_lines(rho=1, theta=np.pi/180, threshold=10, min_line_len=20, max_line_gap=10)\
    .weighted_img(image)\
    .image
</code>

Every pipeline stage takes an input image and provides an output image to the next stage.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function like this:
 - Split the lines in 2 group (left and right), based on the slope sign
 - Estimate the slope of the continous line by taking into consideration the most dominant (longest) ones
 - Once a point and a slope is known, I can easily extrapolate and draw the line to the lower part (y = height) and the higher part (the limit of the region of interest)




### Current problems and possible improvements

While trying to estimate the slope, I realized that some of the short lines cause noise. I experimented a few wasy to compute a weighted slope, but the best results (and smallest code) I got using only the most dominant (longest) lines.

The current solution works well with straight roads, but not with tight curves. This could be fixed using a different extrapolation model (polygon, curve).

In order to remove hardcoded values and to have a scalable solution I assumed the camera is placed in the center of the car/lane.
However, the upper limit of the zone of interest is still hardcoded. This can be solved using information from the detected lanes.

There is a visiblle, but acceptable, jitter of the direction of the line. This can be fixed using information from the previous frames, simplest with a sliding window mechanism.






