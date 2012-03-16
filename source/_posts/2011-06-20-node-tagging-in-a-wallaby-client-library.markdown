---
date: '2011-06-20 11:11:01'
layout: post
slug: node-tagging-in-a-wallaby-client-library
status: publish
title: Node tagging in a Wallaby client library
wordpress_id: '20'
---

In an [earlier post](http://getwallaby.com/2011/using-wallaby-groups-to-implement-node-tagging/), I presented a technique for adding _node tagging_ to Wallaby without adding explicit tagging support to the Wallaby API.  Node tags are useful for a variety of reasons:  they can correspond to informal user-supplied classifications of nodes or machine-generated system attributes (e.g. "64-bit", "high-memory", "infiniband").  Since we implemented tags as special Wallaby groups, they may contain configuration information (although they don't need to) and will be advertised as one of the machine's Condor attributes.

The Wallaby source repository now includes a [small Python module](http://git.fedorahosted.org/git/?p=grid/wallaby.git;a=blob;f=extensions/tagging.py;hb=HEAD) that patches the [Wallaby python client library](http://chapeau.freevariable.com/2010/12/extending-wallaby-with-a-python-client-library.html) with idiomatic tagging support:  namely, you can ask the store for the name of the group that represents the partition between tags and groups, and you can call getTags() and modifyTags() methods on Node objects, as well as inspect the tags property of a Node.  This will appear in Wallaby packages in the future, but you don't have to be that adventurous to try it out now!  To use it, clone the repository and put extensions/tagging.py and schema/wallaby.py somewhere in your PYTHONPATH.  Then follow along with this transcript:

<script src="https://gist.github.com/1035680.js?file=gistfile1.py"></script><noscript>Visit this post in a browser with JavaScript support (not a feed reader) to see the embedded transcript.</noscript>

I appreciate any feedback or comments on this library.

(Cross-posted from [Chapeau](http://chapeau.freevariable.com/).)
