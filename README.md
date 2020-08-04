# Object-Detection-and-Tracking-using-OpenCV

## Description

This is a `OpenCV` implementation of detecting and tracking a baseball across multiple frames. We're given images of 15 consecutive frames in the `images/` folder and we have to find the position and velocity of baseball programatically without using Machine Learning. 

## Approach

### Position of balls

To find the position of the ball in differnet frames, I followed a few steps:

1. Background Subtraction :  Subtract the previous frame from the current frame to get the moving pixels in the current frame. In our case, objects in motion would mainly be the body of the hitter, the bat and the baseball.
2. Hough Circle : Once, we have the areas of interest, then we perfom `HoughCircles` on these regions to look for circular shaped objects (baseball in our case).
3. Filter : In some cases, `HoughCircles` outputs false positives for a baseball at the bottom of the bat, which is also circular. This can be filtered out in a number of ways- by using greyscale intensities or positions. In our case, we use positions, i.e., we introduced a variable `leftmost`, which represents the leftmost cirle and assumes that the ball is always travelling in the left direction and any detection to the right of this variable is ignored. This gives the correct location for the baseball.

### Velocity of balls

I calculate velocity by dividing the Euclidean Distance between points in subsequent frames with the time elpased between frames.

Euclidean distance is calculated using 

                         d(p,q) = \sqrt{(p1-q1)^2 + (p2-q3)^2 + ... + (pn-qn)^2}
where p and q are n-dimesional points of the same object in subsequent frames</center>

<br>
For time elapsed, we take the reciprocal for `fps`, which is 240 in our case

                                              t = (1/fps)

<br>
Finally, velocity is calculated

                                          vel = d(p,q)/t



## Alternatives tried

**Sparse Optical Flow** 

I tried to use Sparse Optical flow to track movement of baseball. I performed the following steps:

1. Used `HoughCircles` on the first frame to get a few circular objects (baseball being one of them)
2. For the next frame, I used `cv2.calcOpticalFlowPyrLK()` to get the optical flow of the selected circular object and only kept the ones with new points(gives the baseball location).
3. Repeated step 2 for all the subsequent frames

**Problem** : After frame 9, it loses track of the baseball and gives wrong predictions. <br>
**Reason** : After frame 9, the motion gets bigger from camera perspective, i.e., the distance between baseball in subsequent frames increases.<br>
**Solutions tried** : Choose pyramid structures with Lucas-Kanade and use larger window size.<br>

I was still unable to improve the predictions with the solutions tried so I gave up on this approach.
