---
date: '2010-10-18 11:59:37'
layout: post
slug: wallaby-node-inventory-with-constraints
status: publish
title: Wallaby node inventory with constraints
wordpress_id: '15'
---

wallaby inventory is a useful command for quickly checking up on the health of your pool and answering certain kinds of questions:  Which nodes have checked in recently?  Which nodes have checked in without being explicitly configured?  Which nodes have never checked in?

Recently, I added _constraint_ support to wallaby inventory.  That is, instead of listing all nodes, you can choose to list only nodes for which a given Ruby expression is true.  These constraints can be simple, like last_checkin < 1.hour_ago (which will find all nodes that have checked in more than one hour ago), or more complex.  They are only limited by Ruby's [$SAFE execution](http://ruby-doc.org/docs/ProgrammingRuby/html/taint.html):  among other things, they cannot modify running classes, perform file I/O, fork new processes, or, because of how QMF is implemented, invoke QMF _methods_ on the node object.  However, every QMF _property_ of the node object is available to wallaby inventory constraints.  The transcript below provides some examples running against a wallaby agent loaded up with test data.

<script src="http://gist.github.com/632556.js"> </script><noscript>Visit this post in a browser with JavaScript support (not a feed reader) to see the embedded transcript.</noscript>

(Cross-posted from [Chapeau](http://chapeau.freevariable.com/).)
