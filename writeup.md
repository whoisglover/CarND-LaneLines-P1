# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)



---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

The first step of my lane finding pipeline is to grayscale the input image.  Next I apply Guassian smoothing with a kernel size of 5. According to the lecture, Guassian smoothing "is essentially a way of suppressing noise and spurious gradients by averaging". This is also built into the Canny edge detection function, but further smoothing first can produce better results. Next up is using the OpenCV function Canny to detect the edges with an upper and lower threshold of 50 and 150, respectively.

Now that I have an image of edges I restrict the image to an area of interest to only focus on the area where the lane lines will be. To keep it simple my initial area of interest is simply a triangle from the bottom left and right with a top point at just over half the image height. 

Next, it is now time to create lines from all the edges I found using the Canny function. I accomplish this by using the HoughLines function. Now I have a list of lines that I can combine to create lane lines. Extrapolating the lanes from the list of lines takes a few steps. First, I filter out lines that have slope that is too steep or too shallow, these are noise that arent really lane lines. Next, I group the lines into left or right by checking the slope (positive slope means the line is in the left lane, negative is in the right). Finally, I use np.polyfit to create a line of best fit for each set of points. This uses linear regression to create the two lines.

Almost done! The last step is to take the two extrapolated lane lines and draw them on the image.

Here are the 8 steps listed in order:

1. Grayscale image
2. Gaussian blur - gray blur image
3. Use canny edge detection to transform the image to edges
4. Find vertices to create an area of interest
5. Restrict the image to the area of interest
6. Use HoughLines to create lines from the edges found in step 3
7. Use the lines found in step 6 to extrapolate whole lane lines
    - First filter out outliers by elminating slopes that are too steep or too shallow 
    - If the slope is positive, the line is part of the left lane so shovel x and y into left_x and left_y points
    - If the slope is negative, the line is part of the right lane so shovel x and y into right_x and right_y points
    - Use np.polyfit to create a line of best fit for each set of points using linear regression, we now have our two lane lines
8. Draw the lines onto the original image
    



![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming with my current pipeline is the simplicity of the area of interest used. I think instead of using a triangle it would be beneficial to use a rhombus.

Another shortcoming is my pipeline seems to break on the challenge video due to some sort of error related to empty input. I think this can be fixed by creating some safety checks in the pipeline. 



### 3. Suggest possible improvements to your pipeline

I would really like to see my pipeleine handle curved lane lines. I think this might be possible by using a polyfit with a higher coefficient.

Another improvement would be to mark the area inside of the lanes as road.
