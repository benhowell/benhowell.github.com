---
layout: post
title: "OpenCV and IP camera streaming with Python"
description: "A simple guide to getting started with the OpenCV computer vision library and IP camera streaming with Python"
tagline: "guide"
category: guide
tags: [opencv, computer vision, python, eye-tracking, webcam, IP camera, streaming, wifi]
related: [Porting Face Analysis SDK to Windows]
article_img: bootstrap/img/AI-ball.jpg
article_img_title: Ai-Ball IP camera
article_img_alt: "An image depicting an AI-ball branded IP camera"
reddit_url:
hn_url:
---
{% include JB/setup %}
<div class="intro">
  <div class="intro-txt">
    <p>
    With todays computing power (including embedded and hobby board computers), the commoditisation of web cameras, and the maturity of computer vision software and object detection algorithms, anyone can play around computer vision for negligible cost.
    </p>
    <p>
    In this guide I'll give you a rough start to streaming content from an IP camera to OpenCV (tested on v2.4.10) for building your own computer vision projects. Although the code in this guide is written in Python there are many other languages supported by OpenCV.
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

#### IP camera streaming into OpenCV
As getting vision from an IP camera into [OpenCV][8] is an unnecessarily tricky stumbling block, we'll only concentrate on the code that streams vision from an IP camera to OpenCV which then simply displays that stream. 
<br />

**webcam-opencv-example.py**
<br />

{% highlight python %}
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

Congratulations, you're now streaming content into OpenCV. NB: Change the url to suit your particular camera.
<br />
<br />

#### Please explain.
I've had trouble with OpenCV and mpeg streams (even though OpenCV has support for this) as was the case in this instance with an [Ai-Ball Wi-Fi camera][2]. The code here deals with the camera's mpeg stream directly and passes each image in that stream to OpenCV for consumption. As each (jpeg) image in the stream is binary encoded and each image frame contains a start marker `'\xff\xd8'` and an end marker `'\xff\xd9'` we can easily detect those markers and segment our stream into individual images. Many other people seem to have a similar problem so there are many other [explanations][3] and [examples][4] out there.
<br />
<br />

#### Examples
Here are a couple of examples of what you might want to do using OpenCV and some very lightweight built-in object detection algorithms (nothing fancy, just some crude knock-ups I've made for demo purposes):

First up, with relatively little extra code, and no other equipment, we can use fiducials to track position and orientation of objects:

**Feature Matching + Homography to find Objects using OpenCV and the ORB (oriented BRIEF) keypoint detector and descriptor extractor.**
Determines the (x,y,z) of the centre point of a marker in order to determine where it is in 3D space relative to the camera.

Details:

 * OpenCV
 * ORB (oriented BRIEF) keypoint detector and descriptor extractor (one of many OpenCV object detection algorithms)
 * Ai-Ball web camera

<div><iframe width="640" height="360" src="https://www.youtube.com/embed/JQUE5RzP4Bo?feature=player_detailpage" frameborder="0" allowfullscreen="1"> </iframe></div>
<br />
<br />

Below is a more complex example that utilises an [SMI Red 500 eye-tracker][5] and [PyViewX][6]. NOTE: Eye-trackers are rapidly becoming a commodity item, and at the time of writing, the [Tobii EyeX developer kit][7] was available for $99USD. I have achieved very good results with this particular eye-tracker and the development SDK (C# only at this point in time) provides gaze and fixation event streams out of the box allowing you to build working models pretty quickly.

**Feature Matching + Homography + Eye Tracking and Gaze Fixation to identify objects and locate them in space.**

Determines fixation start and end points, and for the duration, draws a bounding box around the fixation area of interest (AOI) on the screen. If a recognised marker is within that box (i.e. we're looking at an object) determine the (x,y,z) of the centre point of that marker in order to determine where it is in 3D space relative to the camera. NOTE: The fixation bounding box is for demonstration purposes only. In a real deployment you would not want to display the fixation bounding box as it distracts the user, which in turn changes their gaze point.

Details:

 * OpenCV
 * ORB (oriented BRIEF) keypoint detector and descriptor extractor (one of many OpenCV object detection algorithms)
 * SMI Red 500 eye tracker
 * PyViewX (remote streaming client for SMI eye tracker)
 * Ai-Ball web camera

<div><iframe width="640" height="360" src="https://www.youtube.com/embed/oIL7ftLkxSE?feature=player_detailpage" frameborder="0" allowfullscreen="1"> </iframe></div>
<br />
<br />

#### Opportunities
The purpose of this rough and ready example is to get you started with getting IP camera streams into OpenCV. As shown in the second example in this article, eye-tracking can be easily integrated into computer vision projects and with the present day commoditisation of eye-trackers for the consumer market (including embedded in phones), the application for products combining computer vision and eye-tracking, along with other now commonly available technology like GPS, accelerometers, IMU's, etc. is opening up many new development opportunities in computer vision.




[1]:https://github.com/benhowell/examples/tree/master/WebcamStreamingOpenCV
[2]:http://www.thumbdrive.com/aiball/
[3]:http://stackoverflow.com/questions/21702477/how-to-parse-mjpeg-http-stream-from-ip-camera
[4]:http://stackoverflow.com/questions/26691189/how-to-capture-video-stream-with-opencv-python
[5]:http://www.smivision.com/en/gaze-and-eye-tracking-systems/products/red-red250-red-500.html
[6]:https://github.com/RyanHope/PyViewX
[7]:http://www.tobii.com/buy-eyex
[8]:http://opencv.org/