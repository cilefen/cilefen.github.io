---
layout: post
title:  "Filtering Blank Mail with Procmail"
date:   2012-09-05 12:00:00
categories: procmail
---

If you need to filter messages whose bodies are absolutely empty, use this:

{% highlight bash %}
:0B
* ^^$*^^
blank-mail
{% endhighlight %}