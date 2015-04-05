---
layout: post
title:  "Avoiding recommended and suggested packages (Debian, Ubuntu)"
categories: debian ubuntu avoid-bloat
---

To avoid extra packages (i.e. recommended and suggested) being installed, add a file like say `/etc/apt/apt.conf.d/01norecommend` with this in it:

{% highlight text %}
APT::Install-Recommends "0";
APT::Install-Suggests "0";
{% endhighlight %}
