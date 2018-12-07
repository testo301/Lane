# **Finding Lane Lines on the Road** 

## Writeup Template

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)
[image1]: ./test_images_output/grayScale.jpg "Grayscale"
[image2]: ./test_images_output/gaussianBlur.jpg "Gaussian Blur"
[image3]: ./test_images_output/cannyTransform.jpg "Canny Transformation"
[image4]: ./test_images_output/quadrilateralMask.jpg "Quadrilateral Mask"
[image5]: ./test_images_output/houghLines.jpg "Hough Lines"
[image6]: ./test_images_output/weightedImage.jpg "Weighted Image"

---

### Reflection

### 1. Pipeline Utilized

The pipeline is wrapped within the function process_image() and takes any image (or video frame) as an input.

The image is first converted into the grayscale.

![alt text][image1]

On top of it, Gaussian blurring is applied with the kernel constant defined as 5.

![alt text][image2]

Further, Canny transformation is performed with thresholds 100 and 200 for the lower and upper respectively.

![alt text][image3]

The quadrilateral area of the image is masked, based on the top bottom corners and the middle of the horizon line defined by the horizon_scalar fixed at 1.25. 

![alt text][image4]

Hough lines were extracted with the following set of parameters. The parameters aim at capturing proper coordinates for meaningful shapes on the image.

    rho = 3                 # Resolution for distance on the grid
    theta = np.pi/180       # Radian resolution
    threshold = 50          # Votes for intersections
    min_line_len = 40       # Pixel thresholds for line length
    max_line_gap = 50       # Gap threshold for line connection

![alt text][image5]

The improved draw_lines() function is provided to extrapolate the found lines.

The Hough lines coordinates are used to fit the lines with the first order polynomial. The lines are then divided into ascending and descending shapes. The coordinates and then again grouped into descending and ascending lines and one single line is being fitted for those two respective shapes.

Having two lines only stabilizes the solution.

The images are merged with specified weights.

![alt text][image6]


### 2. Identify potential shortcomings with your current pipeline


The main shortcomings are:
- lack of averaging over time for video. Since draw_lines() function is static and doesn't see the history (given implementation for video is wrapped under other functions), displayed lines are often not stable, especially for intermittent shapes.
- lines for the optional challenge concentrate in the middle of the frame, which can't be explained given proper definition of the quadrilateral region and the horizon constant for line extrapolation that should force the lines to start at the bottom of the frame.
- lack of support for line curvature


### 3. Suggest possible improvements to your pipeline

Potential improvements involve:
- adding averaging of the lines (e.g. simple moving average of the anchor coordinates or lines for the last X frame window) to stabilize the solution. 
- deep dive frame by frame on the optional challenge to identify the reason for lines crossing in the middle of the image.
- better support for intermittent lines
- added support for shape curvature capturing
