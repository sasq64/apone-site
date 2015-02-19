---
layout: default
title: Get Started
---
## Try it
{% highlight bash %}
$ git clone https://github.com/sasq64/apone.git
$ mkdir build
$ cd build
$ cmake ../apone/examples
$ make -j4
$ ./simple
$ ./demo
$ ./snake
{% endhighlight %}

On Raspberry PI you may need to this instead:
{% highlight bash %}
CC=gcc-4.8 CXX=g++-4.8 cmake ../apone/examples
{% endhighlight %}

(Should work on Ubuntu, Mac OS X, Raspberry PI)

## Prerequisites

gcc 4.7+ or clang 3.2+

* glfw3
* zlib
* libcurl

{% highlight bash %}
$ sudo apt-get install cmake git g++ zlib1g-dev libao-dev libgl1-mesa-dev libasound2-dev libglfw3-dev libcurl4-gnutls-dev
{% endhighlight %}

## Documentation

Almost non existant at the moment. Check for `README.MD` files in the different module directories for some information.
