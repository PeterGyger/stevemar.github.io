---
title: "OpenStack Keystone Newton Mid-cycle Recap"
excerpt: "A recap of what went on in the OpenStack Keystone Newton mid-cycle meetup"
tags:
  - software
  - openstack
image:
  path: /images/generic/openstack-logo.png
  thumbnail: /images/generic/openstack-logo.png
---

_Originally posted on [https://developer.ibm.com/opentech/2016/07/28/openstack-keystone-newton-mid-cycle](https://developer.ibm.com/opentech/2016/07/28/openstack-keystone-newton-mid-cycle)_

The OpenStack Keystone Newton mid-cycle took place at the Cisco offices in San Jose. Special thanks goes out to [Chet Burgess](https://twitter.com/cfbIV) and [Morgan Fainberg](https://twitter.com/MdrnStm) for helping me organize and secure a great location. It was a fast-paced three day event that from brought operators and contributors together to focus on the keystone project. We had two days of intense discussion with a final day dedicated to coding working in smaller groups. Also, for the first time, we held a retrospective on the midcycle itself; we discussed non-technical aspects of the previous mid-cycles to determine how effective we are being. This is [summed up](http://dolphm.com/retrospective-on-openstack-midcycles/) by [Dolph Mathews](https://twitter.com/dolphm) on his blog; my recap will pertain to the technical aspects of the midcycle.

## Quick Summary

Over 20 keystone community members attended, including 11 core reviewers. Seven different companies were represented. We collaboratively revised and merged over 20 patches across the identity program’s five main repositories (keystone, keystoneauth, python-keystoneclient, keystone-specs and keystonemiddleware). For a full breakdown of what we discussed see this [etherpad](https://etherpad.openstack.org/p/keystone-newton-midcycle).

## Performance and Scalability

As OpenStack matures, the keystone team has seen a trend in the types of questions we have been asked. Lately, we’ve been fielding a lot of questions about how to tune or deploy Keystone for optimal performance and scalability. Rather than repeatedly give the answer “use fernet tokens and memcache”, we decided to start formal documentation on the subject, which can be viewed [here](http://docs.openstack.org/developer/keystone/performance.html).

## Upgrades

Currently, to upgrade Keystone, a deployer must take all nodes offline, get the latest code, migrate their databases and restart. Ideally, we want to get to a place where rolling upgrades are possible for many Keystone nodes. The previous implementation is unable to meet the requirements of a rolling upgrade and would require downtime. As a result, we are proposing a new upgrade path for Newton (and all future releases). An operator would run a few keystone-manage commands (rolling-upgrade-start, expand, force-upgrade-complete, force-migrate-complete, and contract). This will be very similar to how the Neutron team [manages their upgrades](http://docs.openstack.org/developer/neutron/devref/upgrade.html#server-upgrade). I suspect many other OpenStack teams will begin adopting this pattern. The specification can be viewed [here](https://review.openstack.org/#/c/337680/).

## Multi-Factor Auth

A contentious subject to say the least. Identities in Keystone come from multiple places: SQL, LDAP and Federation. For LDAP and Federation, these identities are stored and managed outside of Keystone, so it makes sense that for features such as PCI and MFA that Keystone *not* be involved. However, Keystone does support storing users in SQL, and we should support using MFA should a deployer desire. There’s currently a [spec up for review](https://review.openstack.org/#/c/345113/), and a [mailing list post](http://lists.openstack.org/pipermail/openstack-dev/2016-July/099419.html). The idea is that a user can authenticate by supplying their password and a TOTP passcode; with the TOTP passcode changing every few minutes. Hopefully we can come to a consensus soon and get this landed early in the Ocata release.

## Long running operations

Long running operations have been a thorn in Keystone’s side for a while. We advocate for a short token lifespan (one hour) - since they are bearer tokens after all. But we find that deployers more often than not set the token lifespan to one day or ever one week. The reason for this is that some operations (snapshotting an image, backing up a large object to swift, etc), can take a *very* long time. We may finally have a solution to this problem, expect a write up from Jamie Lennox in the near future.

## Multi-site deployments

Another question we’re seeing an uptick in is “What’s the best way to manage a multi-region keystone deployment?”. We’ve given the same answer for a number of years, leverage Galera to sync data between sites. However, this manifests into two problems. The first is we have feedback indicating that using Galera for this purpose does not scale for more than ten sites. The second issue is that the Keystone team must be cognizant that 90% of requests to Keystone are for issuing a token, so we should avoid writing to the database for these requests if possible. With Fernet tokens, we are much closer than ever before. We still don’t have a perfect answer, or a true indicator on how many deployers are hitting this issue, but that’s what the summits are for! Thanks for reading, see everyone in Barcelona!
