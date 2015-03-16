---
layout: post
title: "Porting Face Analysis SDK to Windows"
description: "An incomplete, rough and ready guide to building, port, and installation of Face Analysis SDK for Windows"
tagline: "guide"
category: guide
tags: [opencv, computer vision, c++, face tracking]
related: [OpenCV and IP camera streaming with Python]
article_img: bootstrap/img/fasdk.png
article_img_title: Face Analysis SDK by CSIRO Computer Vision Lab
article_img_alt: "An image depicting a face with facial marking overlaid and projected into a computer representation of that face"
reddit_url:
hn_url:
---
{% include JB/setup %}
<div class="intro">
  <div class="intro-txt">
  <p>
    An incomplete, rough and ready guide to building, initial port, and installation of <span markdown="span">[Face Analysis SDK][2]</span> for Windows. The original source requires a few rather specific dependencies and uses a POSIX compliant fork/process model. I'm in now way implying that my solution below is anything but a rough hack to get this stuff running under windows and am certain that my direct translation of POSIX fork() with win32 CreateProcess(...) is suboptimal to say the least. <b>To be clear, this is your starting point, not the solution!</b>
  </p>
  <p>
    This guide has been written in respose to a question I received on youtube about porting the code. <b>NOTE:</b> Whilst the face analysis SDK is open source and the code is freely available to read/use, my personal work is not and I therefore can not share the modified (ported) code base. I can however give an outline of the process needed to start your own port and a bit of code to help you get it done. This is what I have detailed below and if it happens to help someone else in the future then great.
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


#### Dependencies
* Qt 4.x source and build it (yep, it _needs_ to be built from source as there are some bits of functionality not included in the binary). I used Qt4.8.3.
* mingw-gcc440 (yep, it _must_ be this version). This is required to build Qt.
* Python 2.7
* easy_install for Python
* miktex
* cmake
* perl
* OpenCV
* Face Analysis SDK source
<br/>
<br/>

#### Install/build process
* move mingw-gcc440 to C:\mingw
* make your life easier and install cygwin then do all compilation inside using mingw32-make
* install python 2.7
* get easy_install (python)
* easy_install numpy
* easy_install setuptools
* easy_install sphinx
* install miktex
* install cmake
* get eigen and place in 3rd_party_deps folder in opencv
* install perl
* hack path to add mingw32-make and stuff 
* get Qt 4.x source and build it (requires mingw with gcc 440)
* setx -m QTDIR C:/qt
* add qt\bin to PATH 
* cmake opencv, configure mingw32-gcc, mingw32-g++ compilers (requires > gcc 4.6 .. latest mingw). Yes, this means you need to use two different version of mingw to build this thing!


#### Changes required to build face analysis SDK
src/scripts/CMakeLists.txt
remove

* `COMMAND /usr/bin/install -m 755 ${_script} ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${_script}`

add

* `COMMAND install -m 755 ${_script} ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${_script}`

src/test/command-line-options.hpp
add

* `#include <algorithm> //fixes issue with find_if`

src/utils/helpers.hpp

add: 

* `#include <stdarg.h> //fixes issue with va_args`

src/map-list/main.cpp

* port from unix POSIX process/threading model to Win32 (shown below).
<br/>
<br/>

#### Rewriting ForkRunner
A basic, straight port requires rewriting the `ForkRunner` class in `src/map-list/main.cpp`

**Windows port of ForkRunner**
{% highlight cpp %}
class ForkRunner : public Runner
{
public:
  void perform(const std::string &command, const std::list<std::string> &arguments) {
    
    STARTUPINFO si;
    PROCESS_INFORMATION pi;

    ZeroMemory( &si, sizeof(si) );
    si.cb = sizeof(si);
    ZeroMemory( &pi, sizeof(pi) );
    
    if(!CreateProcess(NULL, "", NULL, NULL, FALSE, 0, NULL, NULL, &si, &pi)){ //child process
      char *argv[arguments.size() + 2];
      argv[0] = (char *)command.c_str();
      argv[arguments.size() + 1] = 0;

      std::list<std::string>::const_iterator it = arguments.begin();
      for (size_t i = 0; i < arguments.size(); i++) {
        argv[i + 1] = (char *)it->c_str();
        it++;
      }

      int rv = _execvp(command.c_str(), argv);
      if (rv == -1) 
        throw make_runtime_error("Unable to create new process: %s.", strerror(errno));
    }
    else { // parent process
      throw make_runtime_error("CreateProcess failed (%d).\n", GetLastError() );
      return;
    }
    
    // Wait until child process exits.
    WaitForSingleObject( pi.hProcess, INFINITE );

    // Close process and thread handles. 
    CloseHandle( pi.hProcess );
    CloseHandle( pi.hThread );
  }
};
{% endhighlight %}


There are a couple of import differences too:

**Remove**
{% highlight cpp %}
<#include <sys/wait.h>
<#include <unistd.h>
{% endhighlight %}

**Add**
{% highlight cpp %}
#include <windows.h>
#include <process.h>
{% endhighlight %}


#### Running examples
To demostrate the _"less than real-time"_ speed of this port, I present to you:

**Face Analysis SDK running in Windows with pre-recorded video stream**

<div><iframe width="640" height="360" src="https://www.youtube.com/watch?v=weNiEG0Aq1U" frameborder="0" allowfullscreen="1"> </iframe></div>
<br />
<br />

**Face Analysis SDK running in Windows with live web cam video stream**

<div><iframe width="640" height="360" src="https://www.youtube.com/watch?v=1Y0OXVQY4jc" frameborder="0" allowfullscreen="1"> </iframe></div>
<br />
<br />



[1]:https://github.com/benhowell/examples/tree/master/face_analysis_sdk
[2]:http://face.ci2cv.net/

