---
layout: post
title:  "Toward a Definition of Legacy Code"
date:   2019-11-26 00:00:00
categories: php
---

## Opinions

* "To me, _legacy code_ is simply code without tests...Code without tests is bad code.
It doesn’t matter how well written it is; it doesn’t matter how pretty or
object-oriented or well-encapsulated it is. With tests, we can change the behavior
of our code quickly and verifiably. Without them, we really don’t know if our code is
getting better or worse." —Michael Feathers [^1]
* "Legacy software is any software where people are afraid to make changes." —Rajiv Prabhakar [^2]
* Everyone's definition of _legacy code_ includes "does not have tests".
It is important to understand why legacy code does not have tests. There are at least two reasons. The first reason is that nobody ever bothered to write any (or, nobody on the
project even knew how). The second reason will become obvious when you step in and try to add
tests. _Legacy code resists attempts to test it._ Aggressively. It turns out that the cause
of this resistance is _because_ nobody ever wrote a test. By writing code without even a
thought about testability, the prior developers were destined to write un-testable code.
That's how it goes. All the things that make code un-testable are rightly considered anti-patterns.  —Me

## PHP-Specific

"For our purposes, a legacy application in PHP is one that matches two or more of the following descriptions:

* It uses page scripts placed directly in the document root of the web server.
* It has special index files in some directories to prevent access to those directories.
* It has special logic at the top of some files to `die()` or `exit()` if a certain value is not set.
* Its architecture is include-oriented instead of class-oriented or object-oriented.
* It has relatively few classes.
* Any class structure that exists is disorganized, disjointed, and otherwise inconsistent.
* It relies more heavily on functions than on class methods.
* Its page scripts, classes, and functions combine the concerns of model, view, and controller into the same scope.
* It shows evidence of one or more incomplete attempts at a rewrite, sometimes as a failed framework integration.
* It has no automated test suite for the developers to run.

These characteristics are probably familiar to anyone who has had to deal with a very old PHP application." —Paul M. Jones [^3]

<hr>

[^1]: Feathers, Michael. “Working Effectively with Legacy Code: Michael Feathers: 8601400968741: Amazon.com: Books.” Amazon, Prentice Hall, <a href="https://www.amazon.com/Working-Effectively-Legacy-Michael-Feathers/dp/0131177052">www.amazon.com/Working-Effectively-Legacy-Michael-Feathers/dp/0131177052</a>.
[^2]: Prabhakar, R. (2019, November 26). The Birth of Legacy Software – How Change Aversion Feeds On Itself. Retrieved from https://software.rajivprab.com/2019/11/25/the-birth-of-legacy-software-how-change-aversion-feeds-on-itself/.
[^3]: Jones, Paul M. _Modernizing Legacy Applications In PHP_. Leanpub, 2 Sept. 2013, <a href="https://leanpub.com/mlaphp">leanpub.com/mlaphp</a>.
