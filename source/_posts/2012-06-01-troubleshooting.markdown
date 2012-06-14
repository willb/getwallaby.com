---
layout: post
title: "Troubleshooting Condor with Wallaby"
date: 2012-06-01 12:27
comments: true
---

Often, if you're trying to reproduce a problem someone else is having with Condor, you'll need their configuration.  Likewise, if you're trying to help someone reproduce a problem you're having, you'll want to send along your configuration to aid them in replicating your setup.  For installations that use legacy flat-file configurations (optionally with a local configuration directory), this can be a pain, since you'll need to copy several files from site to site (ensuring that you've included all the files necessary to replicate your configuration, perhaps across multiple machines on the site experiencing the problem).

If everyone involved uses <a href="http://getwallaby.com/">Wallaby</a> for configuration management, things can be a lot simpler:  the site experiencing the problem can use `wallaby dump` to save the state of their configuration for an entire pool to a flat file, which the troubleshooting site can then inspect or restore with `wallaby load`.  If the problem appears in some configuration snapshots but not in others, the reporting site can use [`wallaby vc-export`](http://getwallaby.com/2011/11/exporting-versioned-configurations/) to generate a directory of *all* of their configurations over time, so that the troubleshooting site can attempt to pinpoint the differences between what worked and what didn't.

(Thanks to [Matt](http://spinningmatt.wordpress.com) for pointing out the value of versioned semantic configuration management in reproducing problems!)