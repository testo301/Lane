# **Finding Lane Lines on the Road** 

## Writeup Template

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Pipeline Utilized

The pipeline is wrapped within the function process_image() and takes any image (or video frame) as an input.

The image is first converted into the grayscale.

[image1]: ./examples/grayScale.jpg "Grayscale"

On top of it, Gaussian blurring is applied with the kernel constant defined as 5.

Further, Canny transformation is performed with thresholds 100 and 200 for the lower and upper respectively.

The quadrilateral area of the image is masked, based on the top bottom corners and the middle of the horizon line defined by the horizon_scalar fixed at 1.25. 

Hough lines were extracted with the following set of parameters. The parameters aim at capturing proper coordinates for meaningful shapes on the image.

    rho = 3                 # Resolution for distance on the grid
    theta = np.pi/180       # Radian resolution
    threshold = 50          # Votes for intersections
    min_line_len = 40       # Pixel thresholds for line length
    max_line_gap = 50       # Gap threshold for line connection
  
The improved draw_lines() function is provided to extrapolate the found lines.

The Hough lines coordinates are used to fit the lines with the first order polynomial. The lines are then divided into ascending and descending shapes. The coordinates and then again grouped into descending and ascending lines and one single line is being fitted for those two respective shapes.

Having two lines only stabilizes the solution.

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


The main shortcomings are:
- lack of averaging over time for video. Since draw_lines() function is static and doesn't see the history (given implementation for video is wrapped under other functions), displayed lines are often not stable, especially for intermittent shapes.
- lines for the optional challenge concentrate in the middle of the frame, which can't be explained given proper definition of the quadrilateral region and the horizon constant for line extrapolation that should force the lines to start at the bottom of the frame.
- lack of support for line curvature


### 3. Suggest possible improvements to your pipeline

Potential improvements involve:
- adding averaging of the lines (e.g. simple moving average of the anchor coordinates) for the last X frames) to stabilize the solution.
- deep dive frame by frame on the optional challenge to identify the reason for lines crossing in the middle of the image.
- better support for intermittent lines
- added support for shape curvature capturing
