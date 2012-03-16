---
date: '2010-12-10 09:57:01'
layout: post
slug: extending-wallaby-with-a-python-client-library
status: publish
title: Extending Wallaby with a Python client library
wordpress_id: '17'
---

In [my previous post](http://chapeau.freevariable.com/2010/10/extending-the-wallaby-shell.html), we saw how to extend wallaby by writing Ruby classes that use a client library to extend the wallaby shell.  If you're comfortable with Ruby, this is a great way to build functionality on top of the wallaby API in an idiomatic way.  However, Python programmers shouldn't have to learn Ruby just to interact with wallaby.





The wallaby source repository now includes [a Python client library](http://git.fedorahosted.org/git/?p=grid/wallaby.git;a=blob;f=schema/wallaby.py) to interact with a wallaby service.  This library does a few things to make your life easier:







  1. Raw QMF object proxies are wrapped in Python client objects.


  2. Object references returned by QMF methods are automatically wrapped by client objects.


  3. QMF methods that return error statuses will raise exceptions in the client library.


  4. Client classes include docstrings for QMF methods.





See below for a transcript illustrating these features.  Note that you'll need to have the following packages installed to use the Python wallaby client library:  Red Hat Enterprise MRG 1.3 (or the `python-qmf` package from a recent Fedora release) and Wallaby 0.9.18 or later (0.10.0 to access all of the features exposed through the client library).

<script src="https://gist.github.com/743525.js?file=gistfile1.txt"></script><noscript>Please visit this post in a browser with JavaScript support to view the embedded transcript.</noscript>

(Cross-posted from [Chapeau](http://chapeau.freevariable.com/).)
