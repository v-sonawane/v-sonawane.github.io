# 📷 ROI Segmentation, Contour Detection and Image Thresholding Using OpenCV

{% embed url="https://cdn-images-1.medium.com/v2/resize:fit:1200/1*AWHTU8-GLRJYybq1yfyeOg.gif" %}

OpenCV is a huge open-source library widely used in computer vision, artificial intelligence and image processing domains. The quintessential applications of it in real world are face recognition, object detection, human activity recognition, object tracking and such others.

Now, imagine you have to only detect an object from an entire input frame. So, instead of handling an entire frame, what if you can define a sub-region in the frame and treat it as the new frame to apply the processing. How do we do that you ask? Well, we are going to have a hands-on experience of implementing that in three different stages:

* **Defining Region of Interest(ROI)**
* **Detecting Contours in ROI**
* **Thresholding the frame outlined by detected contours**

Let’s get started, shall we?

### What is ROI? <a href="#13e7" id="13e7"></a>

In simple terms, a sub-region within a frame wherein our object of interest lies is known as Region of Interest(ROI).

### How do we define ROI? <a href="#60d0" id="60d0"></a>

The process of defining the ROI in an input frame is known as **ROI Segmentation**.

In ROI Segmentation, (here) we are selecting a specific region in the frame and providing it’s dimensions in the rectangle method so that it will draw the rectangle-shaped ROI on the frame.

```python
import numpy as np
import cv2
import math

camera = cv2.VideoCapture(0)
camera.set(10,200)


while camera.isOpened():
    ret, frame = camera.read()

    frame = cv2.bilateralFilter(frame, 5, 50, 100)  # smoothening filter
    frame = cv2.flip(frame, 1)  # flip the frame horizontally
    cv2.rectangle(frame, (int(cap_region_x_begin * frame.shape[1]), 0),
                 (frame.shape[1], int(cap_region_y_end * frame.shape[0])), (255, 0, 0), 2) #draw ROI
    cv2.imshow('original', frame) #show the frame

    # Press q to quit
    k = cv2.waitKey(1) & 0xFF
    if k == ord('q'):
        break

# When everything done, release the capture.
cap.release()
cv2.destroyAllWindows()
```

<figure><img src="https://miro.medium.com/v2/resize:fit:1050/1*pPatLyx5dGs4J74iL87hgw.png" alt="" height="571" width="700"><figcaption><p>(Output) Region covered by the blue rectangle is our ROI</p></figcaption></figure>

Now, if you want to bound the object of interest too then we can do that by finding contours in the ROI.

### What is a Contour? <a href="#5438" id="5438"></a>

A Contour means an outline representing or say bounding a shape of an object.

### How to find Contours in the frame? <a href="#81de" id="81de"></a>

For me, finding contours works best after thresholding the ROI frame. So, for finding the contours, the question on the hand is -

### What is Thresholding? <a href="#7c37" id="7c37"></a>

Thresholding is nothing but a simple form of image segmentation. It is a process of transforming a grayscale or rgb image into a binary image. For an instance,

<figure><img src="https://miro.medium.com/v2/resize:fit:476/1*IyNnyak1XQbhe2DhR_rlaA.png" alt="" height="426" width="317"><figcaption><p>(This is a RGB frame)</p></figcaption></figure>

<figure><img src="https://miro.medium.com/v2/resize:fit:456/1*A5zLjHVrxTlf1jxSbylqmA.png" alt="" height="385" width="304"><figcaption><p>(This is a Binary Thresholded frame)</p></figcaption></figure>

So after thresholding the rgb frame, it becomes easy for the program to find contours because since the color of the object of interest in the ROI will be either Black(in simple Binary Threshing) or White(in Inverse Binary Threshing as above), the segmentation(separating background from foreground i.e our object) will be easily done.

After thresholding the frame and detecting the contours, we apply convex hull technique to the set a tight fitting convex boundary around the points of the object. Implementing this, the frame should look like this-

<figure><img src="https://miro.medium.com/v2/resize:fit:422/1*z206cuuM8fQUNQk6c48E0g.png" alt="" height="360" width="281"><figcaption></figcaption></figure>

Another thing we can do is we can mask the ROI to display the object covered by the detected contours itself only. Again-

### What is Image Masking? <a href="#0612" id="0612"></a>

Image masking is a process to hide some portions of an image and to reveal some portions. It is a non-destructive process of image editing. Most of the time it enables you to adjust and tweak the mask later if necessary. Very often, it is efficient and more creative way of the image manipulation.

So, Basically here we will mask the background of the ROI. To do so, first we will fix the background of the ROI. Then, after fixing the background, we’ll subtract it from the frame and replace it with the background wewant(here, a simple black frame).

Implementing above mentioned technique, we should get an output like this-

<figure><img src="https://miro.medium.com/v2/resize:fit:402/1*ZnT8RnuCU-O3gexf-MrPwQ.png" alt="" height="363" width="268"><figcaption><p>(The background is masked as to only capture the object)</p></figcaption></figure>

Here’s the complete code for the desired implementation of the techniques explained.

```python
import cv2
import numpy as np
import copy
import math

x=0.5  # start point/total width
y=0.8  # start point/total width
threshold = 60  # BINARY threshold
blurValue = 7  # GaussianBlur parameter
bgSubThreshold = 50
learningRate = 0

# variables
isBgCaptured = 0   # whether the background captured

def removeBG(frame): #Subtracting the background
    fgmask = bgModel.apply(frame,learningRate=learningRate)

    kernel = np.ones((3, 3), np.uint8)
    fgmask = cv2.erode(fgmask, kernel, iterations=1)
    res = cv2.bitwise_and(frame, frame, mask=fgmask)
    return res

# Camera
camera = cv2.VideoCapture(0)
camera.set(10,200)



while camera.isOpened():
    ret, frame = camera.read()
    frame = cv2.bilateralFilter(frame, 5, 50, 100)  # smoothening filter
    frame = cv2.flip(frame, 1)  # flip the frame horizontally
    cv2.rectangle(frame, (int(x * frame.shape[1]), 0),
                 (frame.shape[1], int(y * frame.shape[0])), (255, 0, 0), 2) #drawing ROI
    cv2.imshow('original', frame)

    #  Main operation
    if isBgCaptured == 1:  # this part wont run until background captured
        img = removeBG(frame)
        img = img[0:int(y * frame.shape[0]),
                    int(x * frame.shape[1]):frame.shape[1]]  # clip the ROI
        cv2.imshow('mask', img)

        # convert the image into binary image
        gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
        blur = cv2.GaussianBlur(gray, (blurValue, blurValue), 0)
        cv2.imshow('blur', blur)
        ret, thresh = cv2.threshold(blur, threshold, 255, cv2.THRESH_BINARY) #thresholding the frame
        cv2.imshow('ori', thresh)


        # get the coutours
        thresh1 = copy.deepcopy(thresh)
        contours, hierarchy = cv2.findContours(thresh1, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE) #detecting contours
        length = len(contours)
        maxArea = -1
        if length > 0:
            for i in range(length):  # find the biggest contour (according to area)
                temp = contours[i]
                area = cv2.contourArea(temp)
                if area > maxArea:
                    maxArea = area
                    ci = i

            res = contours[ci]
            hull = cv2.convexHull(res) #applying convex hull technique
            drawing = np.zeros(img.shape, np.uint8)
            cv2.drawContours(drawing, [res], 0, (0, 255, 0), 2) #drawing contours 
            cv2.drawContours(drawing, [hull], 0, (0, 0, 255), 3) #drawing convex hull
    
        cv2.imshow('output', drawing)

    # Keyboard OP
    k = cv2.waitKey(10)
    if k == 27:  
        camera.release()
        cv2.destroyAllWindows()
        break
    elif k == ord('b'):  # press 'b' to capture the background
        bgModel = cv2.createBackgroundSubtractorMOG2(0, bgSubThreshold)
        isBgCaptured = 1
        print( 'Background Captured')
    elif k == ord('r'):  # press 'r' to reset the background
        bgModel = None
        isBgCaptured = 0
        print ('Reset BackGround')pyth
```

## Let's make some OpenCV magic happen! <a href="#18fd" id="18fd"></a>
