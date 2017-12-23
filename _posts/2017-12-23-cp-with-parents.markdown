---
layout: post
title: "copy files with parents"
date: 2017-12-23 23:37:00 +0800
categories: maven
---
Mac OS X
{% highlight shell %}
find . -name '*.xml' -exec gcp --parents \{\} ../resources \;
{% endhighlight %}

Linux
{% highlight shell %}
find . -name '*.xml' -exec cp --parents \{\} ../resources \;
{% endhighlight %}