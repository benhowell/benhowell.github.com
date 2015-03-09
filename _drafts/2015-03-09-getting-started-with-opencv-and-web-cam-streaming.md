---
layout: post
title: "Getting started with OpenCV and web cam streaming in Python"
description: "A simple guide to getting started with the OpenCV computer vision library and web camera streaming in Python"
tagline: "guide"
category: guide
tags: [opencv, computer vision, python]
related: []
article_img: bootstrap/img/AI-ball.jpg
article_img_title: AI-ball web camera
article_img_alt: "An image depicting an AI-ball branded web camera"
reddit_url:
hn_url:
---
{% include JB/setup %}
<div class="intro">
  <div class="intro-txt">
    <p>
    With todays computing power (including embedded and hobby board computers), the commoditisation of web cameras, and the maturity of computer vision software and object detection algorithms, anyone can play around computer vision for negligible cost. In this guide I'll give you a rough start to streaming content from a webcam to OpenCV (tested on v2.4.10) for building your own computer vision programs. Although the code in this guide is written in Python there are many other languages supported by OpenCV.
    </p>
  </div>
<div class="intro-img-border">
<div class="intro-img-bevel">
<div class="intro-img">
<img class="article-image" alt="{{page.article_img_title}}" title="{{page.article_img_title}}" src="{{ASSET_PATH}}/{{page.article_img}}"/>
</div>
</div>
</div>
</div>
<br/>
<br/>

#### TL;DR
Just give me the code: [GitHub][1]
<br/>
<br/>

### Code
Let's have a look at the code to stream from a webcam to OpenCV.
<br />

**webcam-opencv-example.py**

{% highlight python linenos=table %}
import numpy as np
import cv2
import time
import requests
import threading
from threading import Thread, Event, ThreadError

class Cam():

  def __init__(self, url):
    
    self.stream = requests.get(url, stream=True)
    self.thread_cancelled = False
    self.thread = Thread(target=self.run)
    print "camera initialised"

    
  def start(self):
    self.thread.start()
    print "camera stream started"
    
  def run(self):
    bytes=''
    while not self.thread_cancelled:
      try:
        bytes+=self.stream.raw.read(1024)
        a = bytes.find('\xff\xd8')
        b = bytes.find('\xff\xd9')
        if a!=-1 and b!=-1:
          jpg = bytes[a:b+2]
          bytes= bytes[b+2:]
          img = cv2.imdecode(np.fromstring(jpg, dtype=np.uint8),cv2.IMREAD_COLOR)
          cv2.imshow('cam',img)
          if cv2.waitKey(1) ==27:
            exit(0)
      except ThreadError:
        self.thread_cancelled = True
        
        
  def is_running(self):
    return self.thread.isAlive()
      
    
  def shut_down(self):
    self.thread_cancelled = True
    #block while waiting for thread to terminate
    while self.thread.isAlive():
      time.sleep(1)
    return True

  
    
if __name__ == "__main__":
  url = 'http://192.168.2.1/?action=stream'
  cam = Cam(url)
  cam.start()
{% endhighlight %}

Congratulations.
<br />
<br />

#### Please explain.

I just saw that you mention that you have c++ code that is working, if that is the case your camera may work in python as well. The code above manually parses the mjpeg stream without relying on opencv, since in some of my projects the url will not be opened by opencv no matter what I did(c++,python).

Mjpeg over http is multipart/x-mixed-replace with boundary frame info and jpeg data is just sent in binary. So you don't really need to care about http protocol headers. All jpeg frames start with marker 0xff 0xd8 and end with 0xff 0xd9. So the code above extracts such frames from the http stream and decodes them one by one. like below.




	
bytes is a growing queue that is slowly consumed by the parsing loop, the stream.read(16384) read 16384 bytes of data from http stream and add it to bytes,which will be shortened if a valid jpeg frame is found. the read buffer should be smaller than the smallest jpeg frame size, otherwise, there might be problems.






<br />
<br />

#### Examples
Here are a couple of examples of what you might want to do using OpenCV (nothing fancy, just some crude knockups, ya hear?):

First up, with relatively little extra code, and no other equipment, we could use fiducials to track position and orientation of objects:

**Feature Matching + Homography to find Objects using OpenCV and the ORB (oriented BRIEF) keypoint detector and descriptor extractor.**

<div><iframe width="640" height="360" src="https://www.youtube.com/embed/JQUE5RzP4Bo?feature=player_detailpage" frameborder="0" allowfullscreen="1"> </iframe></div>




Determine the (x,y,z) of the centre point of a marker in order to determine where it is in 3D space relative to the camera.

Details:

 * OpenCV
 * ORB (oriented BRIEF) keypoint detector and descriptor extractor (one of many OpenCV object detection algorithms)
 * Ai-Ball web camera




**Feature Matching + Homography + Eye Tracking and Gaze Fixation to identify objects and locate them in space.**

Determines fixation start and end points, and for the duration, draw a bounding box around the fixation area of interest (AOI) on the screen. If a recognised marker is within that box (i.e. we're looking at an object) determine the (x,y,z) of the centre point of that marker in order to determine where it is in 3D space relative to the camera. NOTE: The fixation bounding box is for demonstration purposes only. In a real deployment you would not want to display the fixation bounding box as it distracts the user, which in turn changes their gaze point.

Details:

 * OpenCV
 * ORB (oriented BRIEF) keypoint detector and descriptor extractor (one of many OpenCV object detection algorithms)
 * SMI Red 500 eye tracker
 * PyViewX (remote streaming client for SMI eye tracker)
 * Ai-Ball web camera

<div><iframe width="640" height="360" src="https://www.youtube.com/embed/oIL7ftLkxSE?feature=player_detailpage" frameborder="0" allowfullscreen="1"> </iframe></div>

<br />
<br />















[1]:https://github.com/benhowell/examples/tree/master/WebcamStreamingOpenCV