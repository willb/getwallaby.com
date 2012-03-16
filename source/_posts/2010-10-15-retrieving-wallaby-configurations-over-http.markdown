---
date: '2010-10-15 13:01:54'
layout: post
slug: retrieving-wallaby-configurations-over-http
status: publish
title: Retrieving Wallaby configurations over HTTP
wordpress_id: '10'
---

In some environments, users may wish to use Wallaby to serve configurations to nodes that can't reach the Qpid broker that the Wallaby agent is running against.  Some users may also want to write simple scripts that access current or historical configuration data without writing a QMF console.  The wallaby http-server command, which is available beginning with wallaby-0.9.19, offers a solution for such users.  It provides a read-only web service gateway to a node's configuration:  the last activated configuration, a particular version, and the node's current configuration in the store (which may not yet have been activated).

wallaby http-server requires that the Sinatra framework is installed.  If you are building RPMs for wallaby, wallaby-http-server will be built as a separate package, and only if you're building from Fedora 12 or later.  If you're installing from source, simply gem install sinatra before running the HTTP server; if you've already installed a wallaby package on a non-Fedora system, you can simply install cmd_http_server.rb [from the source repository](http://git.fedorahosted.org/git/?p=grid/wallaby.git;a=blob;f=lib/mrg/grid/config/shell/cmd_http_server.rb;hb=HEAD) into your wallaby shell commands directory.  (In the future, we expect to package wallaby-http-server for Red Hat Enterprise Linux as well, thus simplifying this process.)

The transcript below shows the HTTP server's built-in help, listing all of the methods it supports (as of wallaby-0.9.20, which will be released later today).  Please let us know if you can think of other API methods that would be useful to expose over the Wallaby HTTP server.

<script src="http://gist.github.com/628571.js"> </script><noscript>Please visit this post in a browser with JavaScript support to see the embedded code snippet.</noscript>

_(Cross-posted from [chapeau](http://chapeau.freevariable.com/2010/10/retrieving-wallaby-node-configurations-over-http.html).)_
