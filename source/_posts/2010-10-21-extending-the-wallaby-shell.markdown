---
date: '2010-10-21 11:43:07'
layout: post
slug: extending-the-wallaby-shell
status: publish
title: Extending the Wallaby shell
wordpress_id: '16'
---

The most recent few releases of the Wallaby configuration management service have included some great new features:  `wallaby console` can now be used as an interpreter for [shebang scripts](http://en.wikipedia.org/wiki/Shebang_(Unix)), `wallaby inventory` now supports limiting the nodes it lists with [expressive constraints](http://chapeau.freevariable.com/2010/10/wallaby-node-inventory-with-constraints.html), the `wallaby http-server` command [provides access to node configurations over HTTP](http://chapeau.freevariable.com/2010/10/retrieving-wallaby-node-configurations-over-http.html), and the `wallaby` command and its subcommands feature many minor functional and aesthetic improvements.  One feature that I'm particularly excited about is under the hood:  there is now an API for extending the wallaby shell with your own subcommands.  In this post, we'll look at how to make a new `wallaby` subcommand.  <noscript>You'll need to view this post outside of a feed reader, in a browser with JavaScript support, in order to see the embedded code snippets.</noscript>

First, make sure you're running [wallaby 0.9.23](http://git.fedorahosted.org/git/?p=grid/wallaby.git;a=shortlog;h=refs/tags/v0.9.23) or later.  If you're running Fedora, you can simply `git clone` the repository, check out the `v0.9.23` tag, and run `rake rpms` to generate installable packages.

Next, you'll want to have a directory in which to put your custom wallaby shell commands.  I recommend putting them somewhere in your home directory.  (For the purposes of this discussion, we'll assume that you're using `~/.wallaby`.)  The `wallaby` command will find and attempt to load every ruby file in this directory that begins with `cmd_`, so you probably don't want it to be writable by anyone other than you.  Once you have this directory set up, set `WALLABY_COMMAND_DIR` in your environment to this directory:

`export WALLABY_COMMAND_DIR=${HOME}/.wallaby`

We'll start by making a fairly straightforward command that simply prints out the API version supported by the wallaby service.  To create a new wallaby command, use `wallaby new-command`:

<script src="http://gist.github.com/638780.js?file=gistfile3.txt"></script>

The `-d` option allows us to provide a description for the new command (viz., what would show up if we typed `wallaby help commands`).  The `-D` option allows us to specify a directory in which to create the new command file.  (`wallaby new-command` supports several other options; you can use `wallaby help new-command` for additional documentation.)  After executing this command, we'll have a documented template file for a new wallaby command, called `cmd_api_version.rb`, installed in our `WALLABY_COMMAND_DIR`.  It will look something like this:

<script src="http://gist.github.com/638780.js?file=generated_cmd_api_version.rb"></script>

We can now run `wallaby help commands` and verify that our command shows up.  Sure enough, it does:

<script src="http://gist.github.com/638780.js?file=gistfile4.txt"></script>

We can even run `wallaby api-version`, although it won't do anything yet.  To make it do something useful, we're going to edit the `act` method defined for us in the `ApiVersion` class.  We could do something very simple, like insert `puts store.apiVersionNumber` before `return 0`, but it would be nice to allow a user to format the API version number as he or she sees fit, in order to use the result of our command in other scripts.

To let the user supply a format string, we'll need to add a command-line option, which requires us to edit the `init_option_parser` method.  This method must return an `OptionParser` object; if you haven't used Ruby's option parser class, read up on [its documentation](http://ruby-doc.org/stdlib/libdoc/optparse/rdoc/classes/OptionParser.html).  For this example, though, the changes we have to make are pretty minor.  

After we've added a command-line option and a body for the `act` method, our `cmd_api_version.rb` file will look more like this:

<script src="http://gist.github.com/638780.js?file=cmd_api_version.rb"></script>

Running this command without arguments will return a string like `The wallaby service is running version 20100915 of the Wallaby API.`  If you supply a format string, you can get simply the raw number (with `--format '%s'`) or something more suited to your taste.  (Note that this example script doesn't do any sanity- or error-checking of the format string, which you'd certainly want to do if you were going to put your command into production.)

We'll look at a more interesting example wallaby shell command and some of the issues that more interesting commands raise in a future post.





(Cross-posted from [Chapeau](http://chapeau.freevariable.com/).)
