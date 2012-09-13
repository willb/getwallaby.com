---
layout: post
title: "Authorization for Wallaby clients"
date: 2012-09-12 17:30
comments: true
categories: 
---

Wallaby 0.16.0, which updates the Wallaby API version to 20101031.6, includes support for authorizing broker users with various roles that can interact with Wallaby in different ways.  This post will explain how the authorization support works and show how to get started using it.  If you just want to get started using Wallaby with authorization support as quickly as possible, skip ahead to the section titled "Getting Started" below.  Detailed information about which role is required for each Wallaby API method is [available here](http://getwallaby.com/api-roles/).

## Overview

Users must authenticate to the AMQP broker before using Wallaby (although some installations may allow users to authenticate as "anonymous"), but previous versions of Wallaby implicitly authorized any user who had authenticated to the broker to perform any action.  Wallaby now includes a database mapping from user names to _roles_, which allows installations to define how each broker user can interact with Wallaby.  Each method is annotated with the role required to invoke it, and each method invocation is checked to ensure that the currently-authenticated user is authorized to assume the role required by the method.  The roles Wallaby recognizes are `NONE`, `READ`, `WRITE`, or `ADMIN`, where each role includes all of the capabilities of the role that preceded it.

If `WALLABY_USERDB_NAME` is set in the Wallaby agent's environment upon startup and represents a valid pathname, Wallaby will use that as the location of the user-role database.  If this variable is set to a valid pathname but no file exists at that pathname, the Wallaby user-role database will be created upon agent startup.  If `WALLABY_USERDB_NAME` is not set, the user-role database will be initialized in memory only and thus will not persist across agent restarts.

### Standard authorization

When Wallaby is about to service an API request, it:

1.  checks the role required to invoke the method.

2.  checks the authorization level specified for the user.  There are several possibilities under which a user could be authorized to invoke a method:
    *  the user is explicitly authorized for a role that includes the required role (e.g. the user has an `ADMIN` role but the method only requires `READ`);
    *  the user is implicitly authorized for a role that includes the required role (e.g. there is an entry for the wildcard user `*` giving it `READ` access and the method requires `READ` access)
    *  the role database is empty, in which case all authenticated users are implicitly authorized for all actions (this is the same behavior as in older versions of Wallaby)
    *  the invocation is of a user-role database maintenance method and the client is authorized via shared secret (see below)

3.  if none of the conditions of the above step hold, the method invocation is unauthorized and fails with an API-level error.  If the API method is invoked over the Ruby client library, it will raise an exception.  If it is invoked via a `wallaby` shell command-line tool, it will print a human-readable error message and exit with a nonzero exit status.

4.  if the user is authorized to invoke the method, invocation proceeds normally.

### Authorization with secret-based authentication

This version of the Wallaby API introduces three new methods:  `Store#set_user_privs`, `Store#del_user`, and `Store#users`.  These enable updating and reading the user-role database; the first two require `ADMIN` access, while the last requires `READ` access.  Because changes in the user-role database may result in an administrator inadvertently removing administrator rights from his or her broker user, Wallaby provides another mechanism to authorize access to these methods.  Each of these three methods supports a special `secret` option in its `options` argument.  When the Wallaby service starts up, it loads a secret string from a file.  Clients that supply the correct secret as an option to one of these calls will be authorized to invoke these calls, even if the broker user making the invocation is not authorized by the user-role database.

The pathname to the secret file is given by the environment variable `WALLABY_SECRET_FILE`.  If this variable is unset upon agent startup, Wallaby will not use a shared secret (and secret-based authorization will not be available to API clients).  It this variable is set and names an existing file that the Wallaby agent user can read, the Wallaby shared secret will be set to the entire contents of this file.  If this variable is set and names a nonexistent file in a path that does exist, Wallaby will create a file at this path upon startup with a randomly-generated secret (consisting of a digest hash of some data read from `/dev/urandom`).  If this variable is set to a pathname that includes nonexistent directory components, the Wallaby agent will raise an error.  If you create your own secret file, ensure that it is only readable by the UNIX user that the Wallaby agent runs as (typically `wallaby`).

### Caveats

The Wallaby agent's authorization support is designed to prevent broker users from altering Condor pool configurations in excess of their authority.  It is not intended to keep all configuration data strictly confidential.  (This is not as bad as it might sound, since Wallaby-generated configurations are available for inspection by Condor users.)  Furthermore, due to technical limitations, it is not possible to protect object property accesses over the API with the same authorization support that we use for API method invocations.  Therefore, if concealing configuration data from some subset of users is important for your installation, you should prevent these users from authenticating to the broker that the Wallaby agent runs on.

## Getting started

Here is a quick overview of how to get started with auth-enabled Wallaby:

1.  Stop your running Wallaby and restart your broker before starting the new Wallaby (this is necessary to pick up the new API methods).  Set `WALLABY_USERDB_NAME` in your environment to a path where you can store the user-role database.  Install and start your new Wallaby.
2.  If you're using the RPM package, it will create a "secret file" for you in `/var/lib/wallaby/secret`.  If not, you will need to set `WALLABY_SECRET_FILE` in the environment to specify a location for this secret file and then restart Wallaby.  The Wallaby secret is a special token that can be passed to certain API methods (specifically, those related to user database management) in order to authorize users who aren't authorized in the user database.
3.  Try using some of the new shell commands:  `wallaby set-user-role`, `wallaby list-users`, and `wallaby delete-user`.
4.  Make sure that you have a secret in your secret file.  Make a note of it.  Try setting the role for your current broker user to `READ` or `NONE` (e.g. "wallaby set-user-role anonymous NONE") and then see what happens when you try and run some other Wallaby shell commands.  You can recover from this by passing the Wallaby secret to "wallaby set-user-role"; see its online help for details.

The default user database is empty, which will result in the same behavior as in older versions of Wallaby (viz., all actions are available to all broker users), but only until a user role is added, at which point all actions must be explicitly or implicitly authorized.

  
  

_This article is cross-posted from [Chapeau](http://chapeau.freevariable.com/2012/09/authorization-for-wallaby-clients.html)_
