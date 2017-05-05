---
layout: post
title:  "Mac OS X Login and Logout Scripts Demystified"
date:   2011-08-15 12:00:00
categories: osx
---

<h2>Update, 2015</h2>
<em>Mac OS X Login scripts are considered <a href="https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CustomLogin.html">deprecated</a></em>. Use them only if you know what you are
doing.

<h2>Before You Begin</h2>
There are some things one must understand about Mac OS X login scripts before you can begin:
<ul>
	<li>Apple refers to them as login- and logout- "hooks".</li>
	<li>Hooks run as root so you need to su as the user to take actions as the user.</li>
	<li>You must activate them with the defaults command or use Workgroup Manager in Open Directory.</li>
</ul>

<h2>Creating a Login Script</h2>
You can technically save your scripts anywhere on the filesystem, but <code>/usr/local/bin</code> makes a lot of sense for various reasons.
So, create a file there and mark it executable:
<pre>sudo touch /usr/local/bin/login
sudo chmod +x /usr/local/bin/login</pre>
<h2>Configuring Login Script Actions</h2>
Open the login script in your favorite editor:
<pre>sudo vi /usr/local/bin/login</pre>
Inside the script, you can do things as root or as the user as shown in this sample batch script:

{% highlight bash %}
#!/bin/bash

##
# Mac login script
##

# As root, create a directory named "/foo"
mkdir /foo

# As root, set or enforce system settings
defaults write ...

# As the user, create a directory named "~/foo"
su - $1 -c "/bin/mkdir -p ~/foo"

# As the user, set or enforce user settings
su - $1 -c "/usr/bin/defaults write ..."</pre>
{% endhighlight %}

The username is passed to the script as the one (and only) argument. In bash, you can use the <code>$1</code> variable to access the username.
<h2>Activating a Login Script</h2>
Run this to activate the script:
<pre>sudo defaults write com.apple.loginwindow LoginHook /usr/local/bin/login</pre>
<h2>Logout Scripts</h2>
Configure a logout script by following the instructions above then activate it as follows:
<pre>sudo defaults write com.apple.loginwindow LogoutHook /usr/local/bin/logout</pre>