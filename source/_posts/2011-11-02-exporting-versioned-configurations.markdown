---
date: '2011-11-02 17:24:33'
layout: post
slug: exporting-versioned-configurations
status: publish
title: Exporting versioned configurations
wordpress_id: '22'
tags:
- configuration
- export
- shell
- wallaby
---

Wallaby stores versioned configurations in a database.  [Wallaby API](http://getwallaby.com/the-wallaby-api/) clients can access older versions of a node's configuration by supplying the `version` option to the `Node#getConfig` method.  Sometimes, though, we'd like to inspect individual configurations in greater detail than the API currently allows.

The [Wallaby git repository](http://git.fedorahosted.org/git/?p=grid/wallaby.git) now contains a [command](http://git.fedorahosted.org/git/?p=grid/wallaby.git;a=blob;f=lib/mrg/grid/config/shell/cmd_versioned_config_export.rb;hb=HEAD) to export versioned configurations from a database to flat text files.  Clone the repository or just download the file and then place `cmd_versioned_config_export.rb` somewhere in your `WALLABY_SHELL_COMMAND_PATH`, and you'll be able to invoke it like this:
    
    Usage:  wallaby vc-export DBFILE
    exports versioned configurations from DBFILE to plain text files
        -h, --help                       displays this message
            --verbose                    outputs information about command progress
        -o, --output-dir DIR             set output directory to DIR
            --earliest NUM               output only the earliest NUM configurations
            --latest NUM                 output only the most recent NUM configurations
            --since DATE                 output only configurations since the given date

It will create a directory (called `snapshots` by default) with subdirectories for each versioned configuration; each of these will be timestamped with the time of the configuration.  Within each version directory, there will be directories for node configurations and stored group configurations.  (If you're using an older version of Wallaby, the only stored group configuration will be for the default group.  Versioned configurations generated with a fairly recent version of Wallaby, on the other hand, will have stored configurations for more groups and should also have useful information about node memberships in the configuration file.)

`wallaby vc-export` allows you both to back up versioned configurations and simply to inspect them.  Let us know if you find a new application for it, or if you find it useful in other ways!
