---
title: A script to test new things in a directory
layout: post
categories:
    - bash
    - letter
tags:
    - bash
---

Here is a script used to detect the changes in one directory since you last accessed.

{% highlight bash %}

#!/bin/bash

#record who you are, check changes for different users
id=`whoami`

if test -e pub-source/tmp/$id; then
	new=`find pub-source/ -cnewer pub-source//tmp/$id`
else
	new=`find pub-source/ -name *` 
fi
touch pub-source/tmp/$id

if test ${#new} -ne 0; then
	echo "${new}" | sed 's#pub-source/##g'
else
	echo "No new!"
fi

{% endhighlight %}
