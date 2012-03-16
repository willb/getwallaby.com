---
date: '2010-10-06 09:56:35'
layout: post
slug: flexible-interaction-with-the-wallaby-console
status: publish
title: Flexible interaction with the Wallaby console
wordpress_id: '9'
---

One of the main benefits of using Wallaby for configuration is the remote-access [API](http://getwallaby.com/the-wallaby-api/).  Because the API is comprehensive and usable from any language with a QMF binding (including C++, Java, Python, and Ruby), it provides developers and users with the means to build tools, policies, templating systems, and one-off scripts on top of Wallaby.

If you're writing a Wallaby API client in Ruby, you can use a couple of nice features to ease development:  the [Wallaby client library](http://git.fedorahosted.org/git/?p=grid/wallaby.git;a=blob;f=lib/mrg/grid/config-client.rb;hb=HEAD), which presents a more polished interface than dealing with raw QMF queries and calls, and the `wallaby console` utility, which connects to a specified broker and gives you a Ruby [REPL](http://en.wikipedia.org/wiki/Read-eval-print_loop) that has a global variable pointing to a store client --- perfect for casual, interactive experimentation.

Use the same global command line options for `wallaby console` as you'd use for any other `wallaby` subcommand:  specifying host, port, and authentication information for the broker.  Once you've started it up, you'll be dropped into a Ruby prompt where `Wallaby::store` is a reference to a client proxy object for your Wallaby store.  You can then inspect or modify any entity that the store knows about; the store object has accessors called `nodes`, `features`, `parameters`, etc., that return arrays of each kind of object.  Then you can invoke methods on each object.  The transcript below shows a session in which the user inspects some details of a node in a trivial pool:

    >> Wallaby::store.nodes.map {|n| n.name}
    => ["frotz"]
    >> frotz = Wallaby::store.nodes[0] ; nil
    => nil
    >> Wallaby::store.groups.map {|g| g.name}
    => ["+++DEFAULT", "+++af413ebf1de0f9d4253eb89717a4e13b"]
    >> Wallaby::store.groups[1].features
    => ["DisablePreemption"]
    >> frotz.getConfig
    => {"PREEMPT"=>"FALSE", "WALLABY_CONFIG_VERSION"=>"1284180448517726"}



In the future, it will be easier to extend the `wallaby` command with your own subcommands.  For now, however, `wallaby console` provides a great way to interact with the Wallaby API.





_(Cross-posted from [Chapeau](http://chapeau.freevariable.com/2010/09/flexible-interaction-with-the-wallaby-console.html).)_
