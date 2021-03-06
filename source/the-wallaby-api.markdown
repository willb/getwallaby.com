---
date: '2010-05-13 17:38:32'
layout: page
slug: the-wallaby-api
status: publish
title: The Wallaby API
wordpress_id: '6'
permalink: /the-wallaby-api/
---

This is the Wallaby API version 20101031.1, which was released as part of Wallaby 0.10.0 on December 14, 2010.  (For older versions of the API, please see [here for the 0.9._x_ API](http://git.fedorahosted.org/git/?p=grid/wallaby.git;a=blob;f=schema/api.txt;hb=63e86724227825cc73df657d83ba9d9fb52d0980) and [here for the 0.3._x_ API](http://git.fedorahosted.org/git/?p=grid/wallaby.git;a=blob;f=schema/api.txt;h=43e75e705a7a22660256fbbf4dfba5f03800e308;hb=b1586024749ac861a9c51f831c00dda5fc02320c).)  The API has a major version and a minor version; successive minor versions may add methods and properties but not change or remove existing members.





## com.redhat.grid.config:Node







  * `name` (`sstr` property)


  * `provisioned` (`bool` property)


  * `last_checkin` (`uint64` property)


  * `last_updated_version` (`uint64` property)


  * `identity_group` (`ref` property)





The object ID of this node's identity group.



  * `memberships` (`list` property)





A list of the groups associated with this node, in inverse priority order (most important first), not including the identity group.



  * `checkConfigVersion(version)`


    * `version` (`uint32`/in)




  * `makeProvisioned()`


  * `getConfig(options)`





    * `options` (`map`/in)





Valid options include 'version', which maps to a version number.  If this is supplied, return the latest version not newer than 'version'.



    * `config` (`map`/out)





A map from parameter names to values representing the configuration for this node.





  * `makeUnprovisioned()`


  * `modifyMemberships(command, groups, options)`





    * `command` (`sstr`/in)





Valid commands are 'ADD', 'REMOVE', and 'REPLACE'.



    * `groups` (`list`/in)





A list of groups, in inverse priority order (most important first).



    * `options` (`map`/in)





No options are supported at this time.





  * `whatChanged(old_version, new_version)`





    * `old_version` (`uint64`/in)





The old version.



    * `new_version` (`uint64`/in)





The new version.



    * `params` (`list`/out)





A list of parameters whose values changed between old_version and new_version.



    * `restart` (`list`/out)





A list of subsystems that must be restarted as a result of the changes between old_version and new_version.



    * `affected` (`list`/out)





A list of subsystems that must re-read their configurations as a result of the changes between old_version and new_version.





  * `checkin()`


  * `explain()`





    * `explanation` (`map`/out)





A structure representing where the parameters set on this node get their values.








## com.redhat.grid.config:Snapshot







  * `name` (`sstr` property)





## com.redhat.grid.config:NodeUpdatedNotice





## com.redhat.grid.config:Feature







  * `name` (`sstr` property)





This feature's name



  * `uid` (`uint32` property)


  * `params` (`map` property)





A map from parameter names to their values as set in this feature



  * `param_meta` (`map` property)





A map from parameter names used in this feature to maps of metadata about those params



  * `conflicts` (`list` property)





A set of other features that this feature conflicts with



  * `depends` (`list` property)





A list of other features that this feature depends on



  * `included_features` (`list` property)





A list of other features that this feature includes, in priority order



  * `modifyDepends(command, depends, options)`





    * `command` (`sstr`/in)





Valid commands are 'ADD', 'REMOVE', and 'REPLACE'.



    * `depends` (`list`/in)





A list of other features a feature depends on, in priority order.  ADD adds deps to the end of this feature's deps, in the order supplied, REMOVE removes features from the dependency list, and REPLACE replaces the dependency list with the supplied list.



    * `options` (`map`/in)





No options are supported at this time.





  * `clearParams()`





    * `ret` (`int64`/out)





0 if successful.





  * `modifyConflicts(command, conflicts, options)`





    * `command` (`sstr`/in)





Valid commands are 'ADD', 'REMOVE', and 'REPLACE'.



    * `conflicts` (`list`/in)





A set of other feature names that conflict with the feature



    * `options` (`map`/in)





No options are supported at this time.





  * `setName(name)`





    * `name` (`sstr`/in)





A new name for this feature; this name must not already be in use by another feature.





  * `modifyParams(command, params, options)`





    * `command` (`sstr`/in)





Valid commands are 'ADD', 'REMOVE', and 'REPLACE'.



    * `params` (`map`/in)





A map from parameter names to their corresponding values, as strings, for this feature.  To use the default value for a parameter, give it the value 0 (as an int).



    * `options` (`map`/in)





No options are supported at this time.





  * `modifyIncludedFeatures(command, features, options)`





    * `command` (`sstr`/in)





Valid commands are 'ADD', 'REMOVE', and 'REPLACE'.



    * `features` (`list`/in)





A list, in inverse priority order, of the names of features that this feature should include (in the case of ADD or REPLACE), or should not include (in the case of REMOVE).



    * `options` (`map`/in)





No options are supported at this time.





  * `explain()`





    * `explanation` (`map`/out)





A structure representing where the parameters set on this feature get their values.








## com.redhat.grid.config:Store







  * `apiVersionNumber` (`uint32` property)





The version of the API the store supports



  * `apiMinorNumber` (`uint32` property)





The minor version (revision) of the API the store supports



  * `host_and_pid` (`list` property)





A tuple consisting of the hostname and process ID, identifying where this wallaby agent is currently running.  (Introduced in 20101031.1)



  * `wallaby_version` (`lstr` property)





A string representation of the version of this wallaby agent.  (Introduced in API version 20101031.1)



  * `checkGroupValidity(set)`





    * `set` (`list`/in)





A set of Mrg::Grid::Config::Group names to check for validity



    * `invalidGroups` (`list`/out)





A (possibly-empty) set consisting of all of the Mrg::Grid::Config::Group names from the input set that do not correspond to valid Mrg::Grid::Config::Groups





  * `addNode(name)`





    * `obj` (`ref`/out)





The object ID of the newly-created Node object.



    * `name` (`sstr`/in)





The name of the node to create.





  * `checkSubsystemValidity(set)`





    * `set` (`list`/in)





A set of Mrg::Grid::Config::Subsystem names to check for validity



    * `invalidSubsystems` (`list`/out)





A (possibly-empty) set consisting of all of the Mrg::Grid::Config::Subsystem names from the input set that do not correspond to valid Mrg::Grid::Config::Subsystems





  * `addParam(name)`





    * `obj` (`ref`/out)





The object ID of the newly-created Parameter object.



    * `name` (`sstr`/in)





The name of the parameter to create.





  * `getFeature(name)`





    * `obj` (`ref`/out)





The object ID of the Feature object corresponding to the requested feature.



    * `name` (`sstr`/in)





The name of the feature to search for.





  * `makeSnapshot(name)`





    * `name` (`sstr`/in)





A name for this configuration.  A blank name will result in the store creating a name





  * `getSubsys(name)`





    * `obj` (`ref`/out)





The object ID of the requested Subsystem object.



    * `name` (`sstr`/in)





The name of the subsystem to find.





  * `activateConfiguration()`





    * `explain` (`map`/out)





A map containing an explanation of why the configuration isn't valid, or an empty map if the configuration was successfully activated.



    * `warnings` (`list`/out)





A set of warnings encountered during configuration activation.





  * `checkNodeValidity(set)`





    * `set` (`list`/in)





A set of Mrg::Grid::Config::Node names to check for validity



    * `invalidNodes` (`list`/out)





A (possibly-empty) set consisting of all of the Mrg::Grid::Config::Node names from the input set that do not correspond to valid Mrg::Grid::Config::Nodes





  * `getNode(name)`





    * `obj` (`ref`/out)





The object ID of the retrieved Node object.



    * `name` (`sstr`/in)





The name of the node to find.  If no node exists with this name, the store will create an unprovisioned node with the given name.





  * `addFeature(name)`





    * `obj` (`ref`/out)





The object ID of the newly-created Feature object.



    * `name` (`sstr`/in)





The name of the feature to create.





  * `addSubsys(name)`





    * `obj` (`ref`/out)





The object ID of the newly-created Subsystem object.



    * `name` (`sstr`/in)





The name of the subsystem to create.





  * `removeFeature(name)`





    * `name` (`sstr`/in)





The name of the feature to remove.





  * `getGroupByName(name)`





    * `name` (`sstr`/in)





The name of the group to search for.



    * `obj` (`ref`/out)





The object ID of the Group object corresponding to the requested group.





  * `checkFeatureValidity(set)`





    * `set` (`list`/in)





A set of Mrg::Grid::Config::Feature names to check for validity



    * `invalidFeatures` (`list`/out)





A (possibly-empty) set consisting of all of the Mrg::Grid::Config::Feature names from the input set that do not correspond to valid Mrg::Grid::Config::Features





  * `removeSnapshot(name)`





    * `name` (`sstr`/in)





A name for the snapshot to remove.





  * `removeParam(name)`





    * `name` (`sstr`/in)





The name of the parameter to remove.





  * `getDefaultGroup()`





    * `obj` (`ref`/out)





The object ID of the Group object corresponding to the default group.





  * `checkParameterValidity(set)`





    * `set` (`list`/in)





A set of Mrg::Grid::Config::Parameter names to check for validity



    * `invalidParameters` (`list`/out)





A (possibly-empty) set consisting of all of the Mrg::Grid::Config::Parameter names from the input set that do not correspond to valid Mrg::Grid::Config::Parameters





  * `removeNode(name)`





    * `name` (`sstr`/in)





The name of the node to remove.





  * `addExplicitGroup(name)`





    * `obj` (`ref`/out)





The object ID of the Group object corresponding to the newly-created group.



    * `name` (`sstr`/in)





The name of the newly-created group.  Names beginning with '+++' are reserved for internal use.





  * `getMustChangeParams()`





    * `params` (`map`/out)





Parameters that must change; a map from names to (empty) default values





  * `removeSubsys(name)`





    * `name` (`sstr`/in)





The name of the subsystem to remove.





  * `validateConfiguration()`





    * `explain` (`map`/out)





A map containing an explanation of why the configuration isn't valid, or an empty map if the configuration was successfully activated.



    * `warnings` (`list`/out)





A set of warnings encountered during configuration activation.





  * `getGroup(query)`





    * `obj` (`ref`/out)





The object ID of the Group object corresponding to the requested group.



    * `query` (`map`/in)





A map from a query type to a query parameter. The queryType can be either 'ID' or 'Name'. 'ID' queryTypes will search for a group with the ID supplied as a parameter. 'Name' queryTypes will search for a group with the name supplied as a parameter.





  * `loadSnapshot(name)`





    * `name` (`sstr`/in)





A name for the snapshot to load.





  * `storeinit(options)`





    * `options` (`map`/in)





Setting 'RESETDB' will reset the configuration database.





  * `getParam(name)`





    * `obj` (`ref`/out)





The object ID of the requested Parameter object.



    * `name` (`sstr`/in)





The name of the parameter to find.





  * `removeGroup(name)`





    * `name` (`sstr`/in)





The name of the group to remove.








## com.redhat.grid.config:Configuration







  * `version` (`uint64` property)





## com.redhat.grid.config:Group







  * `uid` (`uint32` property)


  * `is_identity_group` (`bool` property)


  * `name` (`sstr` property)





This group's name.



  * `display_name` (`sstr` property)





A human-readable version of this group's name, useful for presenting names of identity groups to end-users.



  * `features` (`list` property)





A list of features to be applied to this group, from highest to lowest priority.



  * `params` (`map` property)





A map from parameter names to values as set as custom parameter mappings for this group (i.e. independently of any features that are enabled on this group)



  * `clearFeatures()`





    * `ret` (`int64`/out)





0 if successful.





  * `modifyFeatures(command, features, options)`





    * `command` (`sstr`/in)





Valid commands are 'ADD', 'REMOVE', and 'REPLACE'.



    * `features` (`list`/in)





A list of features to apply to this group, in order of decreasing priority.



    * `options` (`map`/in)





No options are supported at this time.





  * `getConfig()`





    * `config` (`map`/out)





Current parameter-value mappings for this group, including those from all enabled features and group-specific parameter mappings.





  * `addFeature(feature)`


    * `feature` (`lstr`/in)




  * `clearParams()`





    * `ret` (`int64`/out)





0 if successful.





  * `removeFeature(feature)`


    * `feature` (`lstr`/in)




  * `setName(name)`





    * `name` (`sstr`/in)





A new name for this group; it must not be in use by another group.





  * `membership()`





    * `nodes` (`list`/out)





A list of node names from the nodes that are members of this group.





  * `modifyParams(command, params, options)`





    * `command` (`sstr`/in)





Valid commands are 'ADD', 'REMOVE', and 'REPLACE'.



    * `params` (`map`/in)





A map from parameter names to values as set as custom parameter mappings for this group (i.e. independently of any features that are enabled on this group)



    * `options` (`map`/in)





No options are supported at this time.





  * `explain()`





    * `explanation` (`map`/out)





A structure representing where the parameters set on this group get their values.








## com.redhat.grid.config:Parameter







  * `name` (`sstr` property)


  * `kind` (`sstr` property)





The type of this parameter



  * `default` (`lstr` property)





The current default value for this parameter.



  * `description` (`lstr` property)





The description of this parameter.



  * `must_change` (`bool` property)





True if the user must supply a value for this parameter; false otherwise.



  * `visibility_level` (`uint8` property)





The current "visibility level" for this parameter.



  * `requires_restart` (`bool` property)





True if the application must be restarted to see a change to this parameter; false otherwise.



  * `depends` (`list` property)





A set of parameter names that this parameter depends on.



  * `conflicts` (`list` property)





A set of parameter names that this parameter conflicts with.



  * `getRequiresRestart()`





    * `needsRestart` (`bool`/out)





True if the application must be restarted to see a change to this parameter; false otherwise.





  * `setRequiresRestart(needsRestart)`





    * `needsRestart` (`bool`/in)





True if the application must be restarted to see a change to this parameter; false otherwise.





  * `setDescription(description)`





    * `description` (`lstr`/in)





A new description of this parameter.





  * `modifyDepends(command, depends, options)`





    * `command` (`sstr`/in)





Valid commands are 'ADD', 'REMOVE', and 'REPLACE'.



    * `depends` (`list`/in)





A set of parameter names that this one depends on.



    * `options` (`map`/in)





No options are supported at this time.





  * `setKind(kind)`





    * `kind` (`sstr`/in)





The type of this parameter.





  * `modifyConflicts(command, conflicts, options)`





    * `command` (`sstr`/in)





Valid commands are 'ADD', 'REMOVE', and 'REPLACE'.



    * `conflicts` (`list`/in)





A set of parameter names that this parameter conflicts with.



    * `options` (`map`/in)





No options are supported at this time.





  * `setMustChange(mustChange)`





    * `mustChange` (`bool`/in)





True if the user must supply a value for this parameter; false otherwise.





  * `setDefault(default)`





    * `default` (`lstr`/in)





The new default value for this parameter.





  * `setVisibilityLevel(level)`





    * `level` (`uint8`/in)





The new "visibility level" for this parameter.








## com.redhat.grid.config:Subsystem







  * `name` (`sstr` property)


  * `params` (`list` property)





A list representing the set of parameter names that this subsystem is interested in.



  * `modifyParams(command, params, options)`





    * `command` (`sstr`/in)





Valid commands are 'ADD', 'REMOVE', and 'REPLACE'.



    * `params` (`list`/in)





A list representing the set of parameter names that this subsystem should be interested in (for ADD and REPLACE) or should not be interested in (for REMOVE).



    * `options` (`map`/in)





No options are supported at this time.






