---
layout: post
title: "Using the skeleton group"
date: 2012-06-15 12:46
comments: true
categories: 
---

In Wallaby, Condor nodes are configured by applying *features* and *parameter* settings to *groups*.  In order for the group abstraction to be fully general, [Wallaby provides two kinds of *special groups*](http://getwallaby.com/2011/05/using-wallaby-groups-to-implement-node-tagging/):  the *default group*, which contains every node (but which is the lowest-priority membership for each node), and a set of *identity groups*, each of which only contains a single node (and which is always its highest-priority membership, so that special settings applied to a node's identity group always take precedence over settings from that node's other memberships).

The default group provides a convenient mechanism to apply some configuration to every node in a pool, whether or not it has been explicitly configured; this enables us to provide some sensible basic configuration for nodes that are added to the pool opportunistically or from the cloud.  However, for some applications, using the default group may be inflexible because of Wallaby's additive, compositional configuration model.  For example, what if you want to enable some functionality by default but disable it on some subset of nodes?  Or what if you want to use "defaults" to override some settings made in explicit groups?

The *skeleton group* provides a solution to these problems.  (The "skeleton group" is so named by analogy with the [`/etc/skel`](http://www.linuxhowtos.org/Tips%20and%20Tricks/using_skel.htm) directory on Linux systems, which provides a template for the home directories of newly-created users.)  Like the default group, every new node is added to the skeleton group when it is created and receives the last-activated configuration for the skeleton group. Unlike the default group, individual nodes' skeleton group memberships can be at different priorities; furthermore, nodes can be removed from the skeleton group all together.  In this post, we'll see how to set it up and use it.

1. Ensure that you're running Wallaby 0.15.0 or later.  (Some older versions also include experimental support for the skeleton group, but Wallaby 0.15.0 eliminates many rough edges and is the recommended minimum version for using the skeleton group.)
1. Change the environment that your `wallaby-agent` runs in so that `WALLABY_ENABLE_SKELETON_GROUP` is set to `true`.  The easiest way to do this is to modify the `/etc/sysconfig/wallaby-agent` file (on RHEL 5 and 6) or the `/etc/sysconfig/wallaby-agent-env` file (on Fedora).
1. Restart Wallaby by running `service wallaby restart` (on RHEL 5 and 6) or `systemctl  restart wallaby.service` (on Fedora).
1. Create a new node, either explicitly (e.g. `wallaby add-node foo.local.`) or by having a new node check in; note that its memberships include `+++SKEL`.  This is the skeleton group.  (Preexisting nodes won't be in the skeleton group by default, but it's straightforward to add them all via the Wallaby shell.)

Now that you have it set up, here are some things to try doing with it:

1.  Put some of your default-group configuration in the skeleton group.  Note that the skeleton group will be validated in isolation just as the default group is, but the validation procedure for the skeleton group assumes that the default group configuration has also been applied already --- so your skeleton group configuration will need to be valid when applied atop the default group configuration.  (This is so that newly-created nodes will have a valid configuration.)
1.  Put configuration that you want to override on some nodes in the skeleton group.  Then remove these nodes from the skeleton group.
1.  Experiment with configurations that depend on the priority of the skeleton group; move it around in nodes' membership lists.
1.  [Write a Wallaby shell command](http://getwallaby.com/2010/10/extending-the-wallaby-shell/) to copy the configuration from the skeleton group over to a node's identity group and then remove that node from the skeleton group (in order to allow further customization).

Let us know how you wind up using this functionality!