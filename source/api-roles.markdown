---
date: '2012-09-12 17:30:32'
layout: page
slug: api-roles
status: publish
title: User roles required by Wallaby API methods
permalink: /api-roles/
---

## Roles required to invoke API methods

* `Feature#annotation` requires at least `READ` access
* `Feature#clearParams` requires at least `WRITE` access
* `Feature#explain` requires at least `READ` access
* `Feature#modifyConflicts` requires at least `WRITE` access
* `Feature#modifyDepends` requires at least `WRITE` access
* `Feature#modifyIncludedFeatures` requires at least `WRITE` access
* `Feature#modifyParams` requires at least `WRITE` access
* `Feature#setAnnotation` requires at least `WRITE` access
* `Feature#setName` requires at least `WRITE` access
* `Group#addFeature` requires at least `WRITE` access
* `Group#annotation` requires at least `READ` access
* `Group#clearFeatures` requires at least `WRITE` access
* `Group#clearParams` requires at least `WRITE` access
* `Group#explain` requires at least `READ` access
* `Group#getConfig` requires at least `READ` access
* `Group#membership` requires at least `READ` access
* `Group#modifyFeatures` requires at least `WRITE` access
* `Group#modifyParams` requires at least `WRITE` access
* `Group#removeFeature` requires at least `WRITE` access
* `Group#setAnnotation` requires at least `WRITE` access
* `Group#setName` requires at least `WRITE` access
* `Node#annotation` requires at least `READ` access
* `Node#checkConfigVersion` requires at least `READ` access
* `Node#checkin` requires at least `READ` access
* `Node#explain` requires at least `READ` access
* `Node#getConfig` requires at least `READ` access
* `Node#makeProvisioned` requires at least `WRITE` access
* `Node#makeUnprovisioned` requires at least `WRITE` access
* `Node#modifyMemberships` requires at least `WRITE` access
* `Node#setAnnotation` requires at least `WRITE` access
* `Node#whatChanged` requires at least `READ` access
* `Parameter#annotation` requires at least `READ` access
* `Parameter#modifyConflicts` requires at least `WRITE` access
* `Parameter#modifyDepends` requires at least `WRITE` access
* `Parameter#setAnnotation` requires at least `WRITE` access
* `Parameter#setDefault` requires at least `WRITE` access
* `Parameter#setDescription` requires at least `WRITE` access
* `Parameter#setKind` requires at least `WRITE` access
* `Parameter#setMustChange` requires at least `WRITE` access
* `Parameter#setRequiresRestart` requires at least `WRITE` access
* `Parameter#setVisibilityLevel` requires at least `WRITE` access
* `Store#activateConfiguration` requires at least `ADMIN` access
* `Store#addExplicitGroup` requires at least `WRITE` access
* `Store#addFeature` requires at least `WRITE` access
* `Store#addNode` requires at least `WRITE` access
* `Store#addNodeWithOptions` requires at least `WRITE` access
* `Store#addParam` requires at least `WRITE` access
* `Store#addSubsys` requires at least `WRITE` access
* `Store#affectedEntities` requires at least `READ` access
* `Store#affectedNodes` requires at least `READ` access
* `Store#checkFeatureValidity` requires at least `READ` access
* `Store#checkGroupValidity` requires at least `READ` access
* `Store#checkNodeValidity` requires at least `READ` access
* `Store#checkParameterValidity` requires at least `READ` access
* `Store#checkSubsystemValidity` requires at least `READ` access
* `Store#getDefaultGroup` requires at least `READ` access
* `Store#getFeature` requires at least `READ` access
* `Store#getGroup` requires at least `WRITE` access
* `Store#getGroupByName` requires at least `READ` access
* `Store#getMustChangeParams` requires at least `READ` access
* `Store#getNode` requires at least `READ` access
* `Store#getParam` requires at least `READ` access
* `Store#getSkeletonGroup` requires at least `READ` access
* `Store#getSubsys` requires at least `READ` access
* `Store#loadSnapshot` requires at least `WRITE` access
* `Store#makeSnapshot` requires at least `WRITE` access
* `Store#makeSnapshotWithOptions` requires at least `WRITE` access
* `Store#removeFeature` requires at least `WRITE` access
* `Store#removeGroup` requires at least `WRITE` access
* `Store#removeNode` requires at least `WRITE` access
* `Store#removeParam` requires at least `WRITE` access
* `Store#removeSnapshot` requires at least `ADMIN` access
* `Store#removeSubsys` requires at least `WRITE` access
* `Store#storeinit` requires at least `ADMIN` access
* `Store#validateConfiguration` requires at least `READ` access
* `Subsystem#annotation` requires at least `READ` access
* `Subsystem#modifyParams` requires at least `WRITE` access
* `Subsystem#setAnnotation` requires at least `WRITE` access

