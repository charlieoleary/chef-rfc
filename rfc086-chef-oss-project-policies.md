---
RFC: 86
Title: Chef OSS Project Versioning and Release Policies
Author: Tom Duffield <tom@chef.io>
Status: Accepted
Type: Process
Replaces: RFC047, RFC081
---

# Chef OSS Project Versioning and Release Policies

This RFC describes the versioning and release policies of Chef Open Source Software (OSS) projects that fall under the purview of the [Chef Maintainers](https://github.com/chef/chef/blob/master/MAINTAINERS.md).

## Motivation

    As a Chef maintainer,
    I need an established versioning policy,
    so I can version my software consistently.

    As a Chef maintainer,
    I need an established release process,
    so I can make my release consistent.

    As a Chef maintainer,
    I want to be able to release the exact build that I have tested,
    so I do not need to make an additional release build.

    As a Chef user,
    I want to know what the lifecycle policy for software is,
    so I can can understand how the software I consume is managed.

    As a Chef user,
    I need Chef OSS project version numbers to give me expectations about the extent of changes
    so I can estimate my upgrade efforts.

## Specification

### Project Types

This RFC categorizes Chef OSS projects into one of three categories:

  * [Cadence Release](#cadence-release)
  * [Ad-hoc Release](#ad-hoc-release)
  * [Maintenance Mode](#maintenance-mode)

Each component that falls under the purview of the [Chef Maintainers](https://github.com/chef/chef/blob/master/MAINTAINERS.md) falls into one these categories. Which category software falls under will be reflected by the appropriate badge on the top of its `README.md` with a link back to this RFC.

#### Cadence Release

[![](https://img.shields.io/badge/Release%20Policy-Cadence%20Release-brightgreen.svg)](https://github.com/chef/chef-rfc/blob/master/rfc086-chef-oss-project-policies.md#cadence-release)

Chef OSS projects that fall under Cadence Release are under heavy development by the community and Chef Software and are released on a regular cadence. Cadence Releases help ensure that Chef Maintainers get new features and minor bug fixes out to users in a timeline fashion while not overloading the users with numerous releases and monotonically growing version numbers.

Because of the high velocity of development, it is possible for many new features to be added during any given release cycle. To keep the version numbers of [Stable Releases](#stable-release) meaningful, the **MINOR** version will increment by one for each Stable Release. This means that many new features may be added between two Stable Releases, but the **MINOR** version will only increment by one.

Because so much can change from release to release, maintainers will select a `MAJOR.MINOR.PATCH` build as a [Release Candidate](#release-candidate). Maintainers will follow a slightly modified [Release Communication](#release-communication) procedure to make an announcement to the "chef-dev" [Discourse](https://discourse.chef.io) category, notifying users of the impending Stable Release. The maintainers will then wait a pre-determined amount of time to allow user to perform further testing. If no critical regressions or other release blockers are identified, the Stable Release will proceed as scheduled.

The following projects adhere to Cadence Release:

| Chef OSS project | MAJOR Release Cadence | MINOR Release Cadence | Release Candidate Delay |
|---|---|---|---|
| Chef Client | Yearly (April)[<sup>1</sup>](#footnotes) | Monthly (2nd Week) | 3 business days |
| ChefDK | Yearly (April) | Monthly (4th Week)[<sup>2</sup>](#footnotes) | 3 business days |

For projects following the cadence release cycle, no new deprecations will be
added sooner than two months before release. This would mean the end of January
for Chef and ChefDK.

#### Ad-hoc Release

[![](https://img.shields.io/badge/Release%20Policy-Ad--hoc%20Release-green.svg)](https://github.com/chef/chef-rfc/blob/master/rfc086-chef-oss-project-policies.md#ad-hoc-release)

Chef OSS projects that fall under Ad-hoc Release are actively developed by the community and Chef Software but not at such a velocity as to necessitate a cadence release. Instead, software releases will be made on an as-needed basis as determined by the maintainers for that software project.

Unlike Cadence Release projects, maintainers may decide to release multiple **PATCH** artifacts as Stable Releases (e.g. 1.2.3 and 1.2.5 may both be Stable Releases). Communication regarding these releases will still follow the standard Release Communication procedure.

#### Maintenance Mode

[![](https://img.shields.io/badge/Release%20Policy-Maintenance%20Mode-lightgrey.svg)](https://github.com/chef/chef-rfc/blob/master/rfc086-chef-oss-project-policies.md#maintenance-mode)

Chef OSS projects that are in Maintenance Mode are no longer being actively developed but releases are still made in the case a bug fix is required. The details in terms of versioning and release processes are the same as Ad-hoc Releases.

### Supporting Policies and Procedures

#### Versioning Scheme

Chef OSS projects follow a `MAJOR.MINOR.PATCH` versioning scheme based on [Semantic Versioning](https://semver.org). How those segments are incremented is based on the Project Type, but all Chef OSS projects adhere to the following criteria:

Given a version number `MAJOR.MINOR.PATCH`:

  * **MAJOR** version releases (e.g. 1.x -> 2.x) will include breaking or backwards-incompatible changes.
    * _Example: When changing the load order of any cookbook segments_
  * **MINOR** version releases (e.g. 1.1 -> 1.2) will include new features, bug fixes, and will be backwards-compatible to the best of the Maintainers' abilities.
    * _Example: When adding support to the mount provider for special filesystem types that were previously unsupported._
    * _Example: Major version bump of a software dependency._
  * **PATCH** version releases (e.g. 1.1.1 -> 1.1.2) will include backwards-compatible bug fixes.
    * _Example: Automatically bumped for Chef OSS project that is built automatically._
    * _Example: Minor version bump of a software dependency._

When incrementing a Chef OSS project version, the following conditions will apply:

  * When **MAJOR** increases, **MINOR** and **PATCH** will be reset to zero (e.g. 11.X.X -> 12.0.0)
    * _Note: New features that did not exist in version 1.1.0 may be released in 2.0.0 without any intermediary releases._
  * When **MINOR** increases, **PATCH** will be reset to zero (e.g. 11.3.x -> 11.4.0)

Versions are always, with a few exceptions, displayed to the user in the `MAJOR.MINOR.PATCH` format.

    $ chef --version
    Chef Development Kit Version: 1.2.20
    chef-client version: 12.18.31
    delivery version: master (0b746cafed65a9ea1a79de3cc546e7922de9187c)
    berks version: 5.5.0
    kitchen version: 1.15.0

When contextually appropriate, a version may be referred to by only the **MAJOR** or **MAJOR.MINOR** versions. For example:

  * a breaking change: "This behavior will change in Chef 13" would refer to the first release in the `13.MINOR.PATCH` series.
  * a future feature release:: "We expect to release ChefDK 1.3 this month" would refer to the `1.3.PATCH` build that was selected as the Stable Release.

##### Auto-bumping PATCH versions

For Chef OSS projects built using a Continuous Integration / Continuous Delivery (CI/CD) systems, the **PATCH** version of a software product may be increased automatically upon every PR merge by an OpsBot. As not all builds will make it successfully through the CI/CD pipeline, the versions available for public consumption might have gaps (e.g. 1.2.1, 1.2.10, 1.2.11, 1.2.12, 1.2.20).

##### Semantic Versioning Exceptions

In the case where a Chef OSS project that would otherwise fall under the scope of the document does not follow Semantic Versioning, the exceptions will be noted below with a description of the versioning scheme they follow.

| Software | Reasoning |
|---|---|
| [delivery CLI](https://github.com/chef/delivery-cli) | The delivery CLI is a Rust utility bundled with the ChefDK. When it is built, it is always built off of master. It's versioning scheme is based on the Git SHA that was compiled. |

#### Artifact Build

Chef OSS project artifacts available on [https://downloads.chef.io](https://downloads.chef.io) are built using Chef Software's internal CI/CD build system. Depending on the software project, additional artifacts such as Ruby Gems or Docker Images may also be compiled by ancillary build systems and published to their appropriate locations. Instructions for how to install those additional artifacts will be made available on the projects README.

The commit used to generate an artifact will be tagged with the corresponding `MAJOR.MINOR.PATCH` version. The specific formatting of the tag can vary as long as the three version segments are present (e.g. "v1.2.3", "1_2_3", or "1.2.3"). This will allow users, developers, and support personnel to easily navigate the source code associated with a particular build or release.

Some Chef OSS project artifacts (e.g. ancillary gems) are built and published manually by their maintainers.

Some Chef OSS projects will be built following a Continuous Integration / Continuous Delivery (CI/CD) model. This means that every merge to master may result in a build and every successful build that passes functional testing is a candidate for a Stable Release and published for consumption by users. This is especially common for projects that are under Cadence Releases.

Some Chef OSS projects will have very large sets of dependencies that are allowed to float (e.g. ~> version constraint). Doing so allows other projects to consume them without having to match dependencies in lock-step, helping to prevent ecosystem failure when project upgrades before another. Because these dependencies are resolved at build-time, two builds of the same Chef OSS project commit may result in slightly different packages. It is for this reason that we encourage users, when pOSS projectable, to consume pre-compiled binaries through official channels (where this effect is minimized) rather than compiling them themselves.

#### Build Channels

Chef Software maintains three artifact channels: unstable, current, and stable.

  * unstable: Builds that have completed successfully and are awaiting additional automated testing.
  * current: Builds that have passed all automated testing but have not been promoted to stable.
  * stable: Builds that have been manually promoted from current and are considered "ready for production".

#### Release Candidate

A "Release Candidate" is an artifact of a Chef OSS project that is available for download (e.g. available in the _current_ channel) that has been identified as a candidate for a Stable Release.

#### Stable Release

The term "Stable Release" is used to identify a build of a Chef OSS project product that is:

  * consumable on its own (e.g. not shipped only as a part of another software artifact)
  * recommended for use in production environments

The term originated from Chef Software's process of promoting a build from the _current_ channel to the _stable_ channel. However, as not all Chef OSS projects are built and hosted by Chef Software, the announcement of a Stable Release may also refer to the selection of a version as the "recommended" version for use in production settings.

The current Stable Release for a given software product will be reflected at the top README of the Github Repository using a badge (e.g. [![Gem Version](https://badge.fury.io/rb/chef.svg)](https://badge.fury.io/rb/chef))

#### Hot Fix Releases

Chef OSS project maintainers will provide support for critical regressions and [Common Vulnerabilities and Exposures](https://cve.mitre.org/) (CVEs) for the latest `MAJOR.MINOR` and `MAJOR-1.MINOR` Stable Releases.

In the case a Hot Fix Release is required, a release will be fixed following the versioning cadence for a new Stable Release as outlined by the appropriate Lifecycle Policy. Should a fix for the regression be unidentified after an initial investigation period of roughly five hours, the affected versions will be revoked and be made unavailable for download.

In a scenario where a Hot Fix Release is required, the maintainers will decide whether a new **MAJOR**, **MINOR**, or **PATCH** release is necessary based on the scope of the change and the timing of the Hot Fix Release in a Cadence Release cycle. This means that some Hot Fix Releases will result in a second Stable Release for a given **MAJOR.MINOR** version while others may result in a new **MAJOR.MINOR** release.

In all cases, the Chef Maintainers will follow the Release Communication procedures to notify users of the impact and remediation instructions.

#### OpsBot

Chef OSS projects will frequently use bots to perform routine tasks such as DCO compliance checks and automatic version bumping. The specific usage may differ per project, but any commits made to the software project by the OpsBot will be clearly identifiable in the commit history.

#### Release Communication

Notifications regarding Chef OSS projects will follow (roughly) the same notification policy:

  * A message will be posted to either the "chef", "chef-dev", or "Release Announcement" categories on [Discourse](https://discourse.chef.io) as outlined in the Lifecycle Policies below.
  * The Discourse post will be shared in the [#announcements](https://chefcommunity.slack.com/messages/announcements) channel on the [Community Slack](https://communityslack.chef.io)

A message about the promotion of a Stable Release for a Chef OSS project artifact will include (at a minimum) the following information:

  * The version of the artifact being released
  * Release notes that highlight enhancements, bug fixes, or other changes included in the release
  * In the case of a critical regression or security vulnerability, details about the vulnerability including:
    * How to identify if you are affected
    * Immediate remediation steps (if available)
    * Details about the status of a new release to address the issue long term

### Footnotes

  1. Major releases in April avoids releasing during winter holidays, summer vacations, ChefConf and Chef Summits.
  2. Offsetting the Chef and ChefDK releases allows the full attention of the Chef Software development teams on each of those releases and leaves time for any potential hot fixes or follow-up.

## Copyright

This work is in the public domain. In jurisdictions that do not allow for this, this work is available under CC0. To the extent possible under law, the person who associated CC0 with this work has waived all copyright and related or neighboring rights to this work.
