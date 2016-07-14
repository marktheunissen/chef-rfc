---
RFC: unassigned
Author: Daniel DeLeo <dan@chef.io>
Status: Draft
Type: Standards Track
---

# Supermarket Release Channels

Add the ability to assign supermarket artifacts (cookbook versions) to a named
channel, and to filter the universe endpoint data by specifying a channel name.

## Motivation

    As a Supermarket User,
    I want to promote my artifacts through named channels,
    so that I can release them according to my testing and release policy.

Many users implement a testing pipeline to automate testing and release of chef
artifacts. An example of this kind of system is the workflow component of the
Chef Automate product. Frequently it is necessary or convenient to publish an
artifact to a Supermarket service at an early pipeline stage, so that the
artifact can be distributed to other systems for testing. For example, when
using Chef Automate, the default behavior is to publish the artifact in the
build phase, so that it may be retrieved by other systems during acceptance
testing. In the current implementation, publishing the artifact makes it
available to all users of the Supermarket service. For example, a berkshelf
user developing a cookbook may retrieve this artifact when installing
dependencies. This is undesireable because the artifact has not yet passed
acceptance testing and therefore may contain flaws. Additionally, in the case
of Chef Automate, there is a manual release gate that occurs later in the
pipeline; it would be preferable that users only access artifacts that have
passed this gate.

In other artifact storage systems, artifacts may be assigned to channels and
promoted as they pass automated or manual verification. For example, each Chef
Client build is initially created in an "unstable" channel for distribution to
automated testing systems. If the package passes automated testing, it is
promoted to the "current" channel where users may access it.

Adding release channels gives Supermarket users this same ability to control
the release process for their Chef artifacts.

## Specification

#### Association of Artifacts to Channels

Artifacts may belong to an arbitrary number of channels. We expect that in the
case of Chef Automate, users will want artifacts to remain available in the
union channel even after promoting that artifact to rehearsal.

#### Artifact Versions and Channels

Channels may have any number of versions of an artifact, so that old versions
remain available after a newer version is promoted to that channel.

There are some use cases where it is desirable to only allow one version of an
artifact in a channel. Users of Chef Automate, for example, are encouraged to
only use one version of an artifact in a given environment. Thefore, the
user may specify that an artifact (TODO: is this config on the channel, or an
option passed in to the promotion API call?) is _exclusive._ When this option
is active, promoting a version of an artifact to a channel removes all other
versions from that channel.

#### Filtering Artifacts by Channel

Filtering is implemented by modifying the response from the `/universe`
endpoint to include only the artifacts in the requested channel.

TODO: figure out how this works.

For compatibility, there must be a mode of operation where Supermarket works
the same as it does now. One way to do this is to do no filtering if the
channel is not specified. However, it may be desireable to allow the
Supermarket administrator to specify a default channel that is used if none is
specified.

## Downstream Impact

Tools that use the `/universe` endpoint will function correctly unmodified.
However, these tools will need to be modified to allow the user to select the
desired channel in order for users to be able to use the channel functionality.

It is unclear whether this feature would be useful for the public supermarket.
One way to deal with this is to have a feature flag to disable the
functionality and leave it disabled on the public supermarket.

## Copyright

This work is in the public domain. In jurisdictions that do not allow for this,
this work is available under CC0. To the extent possible under law, the person
who associated CC0 with this work has waived all copyright and related or
neighboring rights to this work.
