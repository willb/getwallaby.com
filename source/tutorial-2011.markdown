---
date: '2011-05-03 14:05:44'
layout: page
slug: tutorial-2011
status: publish
title: User Tutorial
wordpress_id: '13'
permalink: /tutorial-2011/
---

This tutorial will walk you through setting up a Condor pool and configuring it with Wallaby.  If you want to experiment with Wallaby and you have an Amazon Web Services account, the easiest way to get started is to instantiate our public AMI with Fedora 14, Wallaby, the configuration tools, and external dependencies installed.  To start up one or several instances of this machine, use Amazon's web console or command-line tools and launch the public image with AMI ID `ami-bade21d3`; you'll log in as `ec2-user`.




If you're going to install on a production pool (and you aren't deploying Wallaby to EC2), the easiest way to proceed is to install from source or packages.  See [getwallaby.com](http://getwallaby.com/) for links to source repositories; packages are available in [Red Hat MRG](http://www.redhat.com/mrg/) and are or will soon be available in Fedora as well.  If you're building from source, you'll need the following packages installed on the main node:






  * Ruby 1.8.6


  * Python 2.5


  * Apache Qpid (with QMF and Python and Ruby bindings for QMF)


  * The SPQR object management library for Ruby


  * The Rhubarb object-graph persistence library for Ruby


  * SQLite and Ruby bindings


  * Wallaby


  * The condor-specific configuration tools, daemons, and libraries (see links on [getwallaby.com](http://getwallaby.com)




Client nodes will only need Qpid and QMF client libraries for Python, the Condor configuration daemon, and (optionally) tools to interact with Wallaby.  (If you want to install the Wallaby shell or other Ruby tools, you should install the Qpid and QMF client libraries for Ruby as well.)




## Setting up




If you're running under the AMI, Wallaby will be running when your machine boots, with a factory database modeling Condor features already installed and ready to use.  If not, you'll need to start Wallaby and load the database.  If you installed Wallaby RPMs (created with `rake rpms` from the Wallaby source repository), then you can execute the following commands to load the factory database:



    
    <code>sudo service start wallaby
    wallaby load /var/lib/condor-wallaby-base-db/condor-base-db.snapshot
    </code>




If not, you can run the `wallaby-agent` command to start Wallaby.  If you run as root, you can have it drop privileges and daemonize; if you run as a regular user, you can choose to run it in the foreground or as a daemon.  See its on-line help for other available options.  Then load the database snapshot file from the source directory with the `wallaby load` command.




You may have noticed that we didn't give a password for the `wallaby load` command.  Wallaby authenticates at the transport layer.  If you've newly-installed a Qpid broker, it will be set up for anonymous access.  In a production environment, you'd want to configure Qpid authentication; see the [Qpid documentation](http://qpid.apache.org/books/0.10/AMQP-Messaging-Broker-CPP-Book/html/index.html) for more details.




Once Wallaby is running, we'll want to set up the Condor configuration daemon to connect to Wallaby and pull down configuration files.  Again, if you're using the AMI, this is aready set up for you.  If not, you'll need to install the `condor_configd` daemon to your Condor `SBIN` directory and then put the distributed `99configd.config` file in your Condor `LOCAL_CONFIG_DIR` directory.




The `configd` configuration defines `LOCAL_CONFIG_FILE` and uses this file to install generated configurations from Wallaby.  It will work on top of your existing legacy Condor configuration, but it must own the parameter setting for `DAEMON_LIST`. That is, if you are interested in changing the value of `DAEMON_LIST`, you must do so through Wallaby.  (This is mainly so that the `configd` can ensure that it is always running on a Wallaby-managed node.)




Bootstrapping the `configd` is quite straightforward:  you simply need to tell it where to find the AMQP broker that Wallaby's QMF interface is published on.    Just create a file in your `LOCAL_CONFIG_DIR` that sets `QMF_BROKER_HOST` to the hostname of the machine that will be running your Qpid broker.  Set this up for every node you want to be configured by Wallaby, pointing each to the same Qpid broker.  Then issue a `condor_restart` command to ensure that the changes you've made take effect.  (This might be the last time you'll explicitly run `condor_restart`, so savor it!)




## Configuring a basic pool




The first thing we'll do when configuring our pool is create a named snapshot so that we can have a baseline to restore if we don't like the results of our configuration.  We'll do this with the `condor_configure_pool` command:



    
    <code>condor_configure_pool --take-snapshot "Baseline"
    </code>




(While this tutorial will primarily focus on the interactive, Condor-specific configuration tools, we could also use the `wallaby make-snapshot` command to make a snapshot, or the `wallaby dump` command to save a snapshot to a file for external backup.)




### Node inventory




We can then see the nodes that have checked in with Wallaby:



    
    <code>condor_configure_pool --list-all-nodes
    </code>




You should see the hostnames of all of the nodes you've set up a `configd` on.  For more detail, we can use the `wallaby inventory` command, which will show us when a node last checked in and whether it is _provisioned_ (that is, we told Wallaby about it explicitly), or whether it is _unprovisioned_ (that is, it checked in and asked for a default configuration).  `wallaby inventory` also supports [constraints](http://chapeau.freevariable.com/2010/10/wallaby-node-inventory-with-constraints.html) to filter node lists; see its documentation for more details.




### Organizing nodes




The easiest way to apply a configuration to multiple nodes is to put those nodes in a group.  We will use the `condor_configure_store` command to create a group:



    
    <code>condor_configure_store -a -g Workers
    </code>




We can then convert our "unprovisioned" nodes to "provisioned" ones by explicitly registering them with Wallaby.  If we were to add nodes `fred` and `barney`, we'd do this:



    
    <code>condor_configure_store -a -n fred,barney
    </code>




This will pop up your `$EDITOR` and let you edit the nodes' memberships.  The format for this file is YAML, and it will look something like this:



    
    <code>--- !Node
    name: fred
    memberships: []
    --- !Node
    name: barney
    memberships: []
    </code>




Add these nodes to the `Workers` group we just created, and save the file.  It will then look like this:



    
    <code>--- !Node
    name: fred
    memberships:
      - Workers
    --- !Node
    name: barney
    memberships: 
      - Workers
    </code>




## Updating configurations




### Adding parameters




The default Wallaby database models many useful parameters, but we may want to add some site-specific ones.  To add a parameter called `TRADESHOW`, we can just use the `condor_configure_store` command:



    
    <code>condor_configure_store -a -p TRADESHOW
    </code>




Again, this will spawn your `$EDITOR` to allow you to edit metadata for the new parameter before going any further.  For more information on what the fields of the Parameter object mean, see the Wallaby API documentation.




### Applying features to groups




Once we have all of the parameters and features that we care about modeled in Wallaby, we can begin applying features to groups.  We'll start by adding a `Master` feature to the default group, which will ensure that every node in the pool is running a Condor master:



    
    <code>condor_configure_pool --default-group -a -f Master
    </code>




In general, when we add features to groups, the `condor_configure_pool` command may ask us to set values for required parameters.  Do so as necessary, but do not yet activate the configuration.




We can then set up the nodes in the `Workers` group as execute nodes and update a parameter value on these.  Say, for example, that we'd like a 30 second update interval:



    
    <code>condor_configure_pool -g Workers -a -f ExecuteNode -p UPDATE_INTERVAL
    </code>




Enter 30 when prompted for the `UPDATE_INTERVAL` value; again, do not yet activate the configuration.  






Finally, we'll set up a Condor central manager and a `schedd` on one of our nodes.  Again, assuming that our nodes are named `fred` and `barney`, we'd do it this way to set up `fred` as our central manager and `schedd`:



    
    <code>condor_configure_pool -n fred -a -f CentralManager,Scheduler
    </code>




Now we can activate the configuration.  Unfortunately, it fails to validate; we haven't set up access policies for the nodes in our pool.  We can add the missing dependency by adding the `NodeAccess` feature to the default group in our pool:



    
    <code>condor_configure_pool --default-group -a -f NodeAccess
    </code>




Set the access policy as appropriate for your situation; if you're just testing on a pair of temporary virtual machines, you can set fairly liberal access control lists (or even `*`).  In production, you'll want to be more restrictive.  Notice that when our configuration is successfully activated, Wallaby automatically generates a named snapshot.  Let's make sure that we have that snapshot:



    
    <code>condor_configure_pool --list-all-snapshots
    </code>




### Making changes




Let's set up our `Workers` group to enable dynamic slots.  We'll also take a snapshot of the changes we've made up to this point.



    
    <code>condor_configure_pool -g Workers -a -f DynamicSlots
    </code>




When this completes, create a named snapshot called "Before Custom" and activate the configuration.




Now we can add a parameter to the `Workers` group.  Let's say that these workers are going to be used for a demo at Condor Week 2011, so we'll set `TRADESHOW` to `CondorWeek2011`.



    
    <code>condor_configure_pool -g Workers -a -p TRADESHOW
    </code>




Set the parameter's value, but do not activate the configuration yet.  Perhaps we're feeling ambitious (or, if we're running AMIs, don't mind running an instance for a year) and think that one of our nodes will still be in service next year.  We can set `TRADESHOW` to a different value in its identity group, which will supercede the definition from the `Workers` group:



    
    <code>condor_configure_pool -n barney -a -p TRADESHOW
    </code>




Set this to `CondorWeek2012` and activate the configuration.  You should then be able to see that `TRADESHOW` has different values on `fred` and `barney` by using `condor_config_val TRADESHOW`.




## Inspecting configuration




Let's say we want to examine `fred`'s configuration:




condor_configure_pool -n fred -l




If we want the full configuration as pushed out to the node, we can pass `-v` to `condor_configure_pool` as well.  We can also see that the Wallaby features and groups for each node are advertised as ClassAd attributes, in case you'd like to use these in matchmaking:




condor_status fred -l | grep Wallaby




## Loading snapshots




If we want to return to a previous configuration, we can load an older snapshot.  Doing this restores the state of the Wallaby service, but does not activate, validate, and push the configuration.  After loading a snapshot, make any changes you wish and then activate the configuration.  Some examples follow.




To restore to the "Baseline" snapshot, we'd do this:




condor_configure_pool --load-snapshot "Baseline"
   condor_configure_pool --activate




To remove the `TRADESHOW` param, we'd do this:




condor_configure_pool --load-snapshot Before Custom
   condor_configure_pool --activate




## Configuring unprovisioned nodes




We can ensure that nodes that are added to our pool have useful configurations by setting up the default group properly.  If we set up the default group as an execute node, new nodes will automatically be configured as execute nodes.




First, we'll delete the `Workers` group, since every node will now be an execute node.  This will not delete the nodes in the group; it will just delete the group and its configuration.



    
    <code>condor_configure_store -d -g Workers
    </code>




We'll then set up the default group as an execute node, the way we did for the `Workers` group above:



    
    <code>condor_configure_pool --default-group -a -f ExecuteNode -p UPDATE_INTERVAL
    </code>




Activate this configuration and add a node to the pool, configuring its `QMF_BROKER_HOST`.  The node should check in and will be configured as an execute node.




## Next steps




You can use the interactive tools to define new features or parameters.  You may also want to investigate the _Wallaby shell_ (try running `wallaby help commands` for more information) and the Wallaby API, which features idiomatic client libraries for Ruby and Python.  For a quick introduction to both the shell and the API, see [this tutorial](http://chapeau.freevariable.com/2010/10/extending-the-wallaby-shell.html) on extending the wallaby shell.
