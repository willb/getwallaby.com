---
layout: post
title: "Highly-available configuration data with Wallaby"
slug: live-backup
date: 2013-01-24 09:40
comments: true
categories: 
---

Many Condor users are interested in _high-availability_ (HA) services:  they don't want their compute resources to become unavailable due to the failure of a single machine that is running an important Condor daemon.  (See [this talk](http://research.cs.wisc.edu/condor/CondorWeek2012/presentations/rati-benton-condor-ha.pdf) that Rob Rati and I gave at Condor Week this year for a couple of solutions to HA with the Condor `schedd`.)  So it's only natural that Condor users who are interested in configuring their pools with [Wallaby](http://getwallaby.com) might wonder how Wallaby responds in the face of failure.

Some of the technologies that the current version of Wallaby is built upon do not lend themselves to traditional active-active high-availability solutions.  However, the good news is that due to Wallaby's architecture, almost all running Condor nodes _will not be affected_ by a failure of the Wallaby service or the machine it is running on.  Nodes that have already checked in with Wallaby will have their latest activated configurations as of the last checking.  The only limitations in the event of service failure are:

1. new nodes will not be able to check in and get the default configuration; 
2. it will not be possible to access older activated configurations; and
2. it will not be possible to alter, activate, or deploy the configuration.

These limitations, of course, disappear when the service is restored.  For most users, losing the ability to update or deploy configurations due to a service failure — but not losing their deployed configurations or otherwise affecting normal pool operation — represents an acceptable risk.  Some installations, especially those who aggressively exploit Wallaby's scripting interface or versioning capability, may want a more robust solution:  these users might want to be able to access older versions of their activated configurations even if Wallaby is down, or they might want a mechanism to speed recovery by starting a replica of their service on another machine.  In the remainder of this post, we'll discuss some approaches to provide more access to Wallaby data when Wallaby is down.

### Accessing older versioned configurations

If you need to access historical versioned configurations, the easiest way to do it is to set up a `cron` job on the machine running Wallaby that periodically runs `wallaby vc-export` on your snapshot database and outputs versioned configurations to a shared filesystem.  `wallaby vc-export`, which is documented [in this post](http://getwallaby.com/2011/11/exporting-versioned-configurations/), exports all historical snapshots to plain-text files, so you can access the configuration for `foo.example.com` at version 1234 in a file called something like `1234/nodes/foo.example.com`.  This `cron` job needs to be able to access the filesystem path of the Wallaby snapshot database; furthermore, to run it efficiently, you'll probably want to limit the number of snapshots it processes each time; see `wallaby vc-export`'s online help for more details.

### Exporting Wallaby state to a file

If you're just interested in the state of the Wallaby service (including possibly unactivated changes), you can periodically run `wallaby dump` over the network.  This will produce a YAML file consisting of the serialized state of the Wallaby; you can later load this file by using the `wallaby load` command, possibly against another Wallaby agent.

### Backing up the raw database files

The easiest way to pick up and recover from a Wallaby node failure is to start a new Wallaby service with the same databases as the failed node.  In turn, the easiest way to do this is by periodically copying these database files from their locations on the Wallaby node (typically in `/var/lib/wallaby`) to some location on a shared filesystem.  The following Ruby script<noscript>, which you'll need to view this post in a browser and not a feed reader to see,</noscript> will safely copy the SQLite files that Wallaby uses even while Wallaby is running:

<script src="https://gist.github.com/3518892.js?file=backup-db.rb"></script>

(cross-posted from [Chapeau](http://chapeau.freevariable.com/2012/08/highly-available-configuration-data-with-wallaby.html))