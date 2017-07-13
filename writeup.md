# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. My image processing pipeline consisted of the following 6 steps: RGB --> Grayscale --> Canny Edge detection -->
Blurring --> Masking --> Hough transform --> Redrawing line on weighted original. All 6 resulting images are included above.
For the video part, however, I omitted the conversion to Grayscale part, as brightness levels (luminance?) on parts of the road in the Challenge video were similar to lane markings.
For the draw-lines() function, after picking up all lines in the region of interest, I selected the lines with a slope from -0.3 to -4 as Left lane markers, and lines with a slope of 0.3 to 4 as Right lane markers. This omits "flattish" lines with a slope ~0 and ~vertical lines. Lines with a y-intercept greater than image height were chosen as left markers, and those with y-intercept below 50% of image height were chosen as right lane markers. I averaged the slope and y-intercepts of the resulting left and right markers to create two lines extending from the bottom of the image to 60% of image height. (rather than hard numbers coded in, wherever possible, I tried to scale to image size here & in region-of-interest, to improve robustness to arbitrary image sizes.

### Potential Shortcomings of pipeline
1. Computationally intensive: for real-time processing, I felt my pipeline was rather slow; it needs to be more efficient.
2. Shadows, variation in lighting & road surface/ color: For the video processing I omitted the grayscale conversion as it was causing problems in the challenge video. A grayscale rendition would obviously help in computation efficiency.
3. Proximity of shoulder: If the shoulder is too close to the lane marking, my pipeline will be confused by the Canny edge created by that, and since I'm averaging lines, the drawn lines would be wider than appropriate.
4. Driving in traffic: Large bright vehicles/objects, both near and far will probably introduce artifacts, posing a problem for correct lane line identification.
5. Sharp curves: Wiggly roads and sharp curves will have issues since I'm doing a 1inear extrapolation.

### Improvements
I think filtering out a range of colors around the colors used for lane markings would help. Smoothing was a very big challenge for me, as frames where a lane marking was not detected first gave me NaN errors(!!!), and then bouncing lines. I had thought of averaging over 4-5 frames, but only belatedly figured out a way to do it! I "cheated" to see what others had done, and used a weighted sum of just 2 frames (current & previous).
Finally, I think my code needs to be able to handle errors more robustly. I suspect that if lane markings are washed out for a number of frames, the pipeline may crash. I plan to run this pipeline on more extreme/ fringe case situations.

### Follow-up to reviewer feed-back
My intention had been to use as similar parameters for the 6 images and for the video (for Hough, blur & Canny). In processing video I had hoped to use as much blur I could do in hopes of reducing computation load. Hence kernel_size up to 11, and rho of 4. I reduced both of these to 7 & 2 respectively in line with reviewer's suggestion.
I nudged up min_line_length to 80 from 40 previously, but left threshold at 50. I had discovered that I had to lower these parameters in order to eliminate NaN errors that I believe were coming from frames where there were no lines, and thus slopeMean would go to zero, resulting in divide by zero errors. As mentioned in the improvement section, I would have liked to make the code more resilient when faced with such math errors; this is the reason for the try/except lines for some of the calculations. PS: the shortcomings/improvements sections were already there in my submission; I've highlighted the label headings just to help.
