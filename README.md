# **Finding Lane Lines on the Road** 

<img src="examples/laneLines_thirdPass.jpg" width="480" alt="Combined Image" />

Overview
---

When we drive, we use our eyes to decide where to go.  The lines on the road that show us where the lanes are act as our constant reference for where to steer the vehicle.  Naturally, one of the first things we would like to do in developing a self-driving car is to automatically detect lane lines using an algorithm. In this project I used Python and OpenCV to detect lane lines on images and video of recorded from highway driving.

The Project
---

## If you have already installed the [CarND Term1 Starter Kit](https://github.com/udacity/CarND-Term1-Starter-Kit/blob/master/README.md) you should be good to go!   If not, you should install the starter kit to get started on this project. ##

**Step 1:** Set up the [CarND Term1 Starter Kit](https://classroom.udacity.com/nanodegrees/nd013/parts/fbf77062-5703-404e-b60c-95b78b2f3f9e/modules/83ec35ee-1e02-48a5-bdb7-d244bd47c2dc/lessons/8c82408b-a217-4d09-b81d-1bda4c6380ef/concepts/4f1870e0-3849-43e4-b670-12e6f2d4b7a7) if you haven't already.

**Step 2:** Open the code in a Jupyter Notebook

I completed the project code in a Jupyter notebook.  If you are unfamiliar with Jupyter Notebooks, check out <A HREF="https://www.packtpub.com/books/content/basics-jupyter-notebook-and-python" target="_blank">Cyrille Rossant's Basics of Jupyter Notebook and Python</A> to get started.

Jupyter is an Ipython notebook where you can run blocks of code and see results interactively.  All the code for this project is contained in a Jupyter notebook. To start Jupyter in your browser, use terminal to navigate to your project directory and then run the following command at the terminal prompt (be sure you've activated your Python 3 carnd-term1 environment as described in the [CarND Term1 Starter Kit](https://github.com/udacity/CarND-Term1-Starter-Kit/blob/master/README.md) installation instructions!):

My code if found in P1.ipynb.

My Pipeline
---

I developed a pipeline that built on the process laid out over the course of the prior lesson. This involved five steps. First the imagery was loaded and converted to grayscale. Then I preprocessed the image with a Gaussian blur to dull any exceedingly sharp edges. This was then given to the Canny edge detection function to identify the edges present. The grayscale conversion to Canny edge detection was handled within the same line.
At this point the image was prepped for line-finding. A trapezoidal mask corresponding to the approximate area where the lane was anticipated to be was created. This allowed the program to reject any excessive edges that didn’t correspond to the lane lines we were interested in. This was then fed into a Hough transformation function to turn the Canny points into lane lines. The transformation required a minimum threshold of 5 and a minimum line length of 10. This allowed for some additional “noise” in the image to be filtered out.

The Hough transformation developed short line segments. Each of these line segments were checked for slope. The pointes defining segments with a positive slope were put in a list for the right lane marker, and the negative slopes were put in a list for the left. These points were then fit to a first order polynomial function using numpy.polyfit() to define the left and right lane lines. Then using the top and bottom y-axis values of the trapezoid defining our area of interest the x-values for the start and end points of each lane-line segment were solved.

Running this pipeline over the video files it was found that the lines overlaid on the video were quit jittery. Going back and reanalyzing the pipeline it was found that occasionally a line segment along one side of the image would have a differing slope. For example: a positive slope despite being on the left side. An additional filtering step was implemented that only retained points on the list that were within one standard deviation. These filtered points were then fed into the polyfit function and the resulting output was much more stable.

Potential Shortcomings and Improvements
---

This feels like somewhat of a brute-force approach. There are four FOR loops in the code, two of which make up a nested pair. While not terribly slow, there was a discernable lag in the time it took the video file to be processed. This lag would be problematic in a self-driving car. Secondly, while noticeably better, the line markers are still slightly jittery and barely work on the challenge video. This might be due to the curve invalidating the assumption of a first order polynomial. Instead of reconstructing a polynomial to fit to the lane lines, use an estimation technique to filter out the outlier points and to estimate coefficients of the polynomial.
