---
date: '2010-10-06 09:32:01'
layout: post
slug: migrating-legacy-condor-configurations-to-wallaby
status: publish
title: Migrating legacy Condor configurations to Wallaby
wordpress_id: '8'
---

[Wallaby](http://getwallaby.com) provides a great way to manage Condor configurations, and if you're just starting out with Condor, it's easy to do things the Wallaby way from the start.  However, many installations have their own systems for managing Condor features and configuration files --- perhaps with separate files representing common, node-specific, and feature-specific configurations.  In this post, we'll talk about wallaby feature-import, a command that lets you import Condor configuration-file snippets into the Wallaby database as Wallaby features, complete with metadata and relationships with other features.





The basic syntax of the file you'll pass to wallaby feature-import is the same as the basic syntax of a Condor configuration file; in fact, you can import many Condor configuration files without modification.  (Only the NAME = VALUE syntax is supported at this time; you can't use the NAME = +VALUE syntax for DC_DAEMON_LIST.)  However, there are some special directives that you can pass to the configuration file as comments:

    #name NAME

Indicates that this feature should be named _NAME_.  (Note that you must supply a name for the feature, either on the command line, via -n NAME, or in the file itself.)

    #includes FEATURE

Indicates that this feature should include _FEATURE_, which must exist in Wallaby.

    #dependson FEATURE

Indicates that this feature should depend upon _FEATURE_, which must exist in Wallaby.

    #default PARAM

Indicates that this feature should include _PARAM_, and set it to its default value.

Because Condor ignores these comments, it is possible to use the same file both as a Condor configuration file and as a Wallaby feature description.  (Other comments will be ignored by wallaby feature-import, but they may generate harmless warnings.)  wallaby feature-import will automatically create any parameters referenced in the file that don't already exist in Wallaby's database; this can be a great time-saver for migrating to Wallaby from a custom configuration-management system.

The following example file is a feature that defines eight startds on a single node:

<script src="http://gist.github.com/599099.js?file=EightStartds.txt"></script><noscript>Please visit this post in a browser with JavaScript support to see the embedded code sample.</noscript>

_(Cross-posted from [Chapeau](http://chapeau.freevariable.com/2010/09/migrating-legacy-condor-configurations-to-wallaby.html).)_
