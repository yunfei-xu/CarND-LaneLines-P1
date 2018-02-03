# **Finding Lane Lines on the Road**

The goals / steps of this project are the following:

- Make a pipeline that finds lane lines on the road
- Reflect on your work in a written report


[//]: # (Image References)

[image0]: ./intermediate_images/0-original.jpg "Original"
[image1]: ./intermediate_images/1-gray.jpg "Gray"
[image2]: ./intermediate_images/2-blurred.jpg "Blurred"
[image3]: ./intermediate_images/3-canny.jpg "Canny"
[image4]: ./intermediate_images/4-roi.jpg "ROI"
[image5]: ./intermediate_images/5-final.jpg "Final"


---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. Given an original image

![alt text][image0]

- First, I converted the image to grayscale

![alt text][image1]

- then I blurred the image with Gaussian kernel `kernel=5`

![alt text][image2]

- then I ran Canny edge detection algorithm with `low_threshold=50` and `high_threshold=150`

![alt text][image3]

- then I limited the region of interest to a polygon

![alt text][image4]

- then I found lines using Hough Transform with `rho=1`, `theta=np.pi/180`, `threshold=20`, `min_line_len=10`, and `max_line_gap=10`. Overlay the lines on top of the original image gives

![alt text][image5]

---

In order to draw a single line on the left and right lanes, I modified the draw_lines() function. First separate the end points of all line segments into left and right point sets, by looking at the slope of each line. If the slope is less than zero, it belongs to the left point set, otherwise it belongs to the right set. Next I fit each set of points into a line using RANSAC from `scikit-learn` module which is good for data with a lot of outliers. Then I found the end point of the predicted line by using the learned linear model.


### 2. Identify potential shortcomings with your current pipeline

Here are some shortcomings of using this simple pipeline:

- The region of interest could vary a lot depending on the camera position, the hard-coded ROI with respect to the image size cannot generalize well for other data

- Most of the times the lanes are not straight, especially in the urban area, this pipeline does not work if the lanes are sharply curved

- This approach seems only good at detecting lanes when the road is clear. But often there are many traffic participants on the road, this simple approach may identify some car's boarder as lines.

- There are often cases where lane markers are not clear, broken, or may not exist at all. The current approach cannot handle this well.

- From the optional challenge, it seems the current pipeline doesn't work well when the background color changes. For example, there are part of the road newly paved. Or part of the road is under tree shadow.


### 3. Suggest possible improvements to your pipeline

Therefore, we need more robust approach to detect lanes. Some possible improvements include:

- Using polynomials instead of straight lines to fit the lane markers

- Converting the image into bird-eye view using the camera calibration parameters

- Using vehicle dynamics or history images to track the lanes

- Or completely using a different approach. For example, training a deep neural network with a lot of labeled images
