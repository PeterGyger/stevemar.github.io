---
title: "Keystone mid-cycle recap for Liberty"
excerpt: "A recap from the keystone liberty mid-cycle in Boston"
tags: 
  - software
  - openstack
image:
  path: /images/openstack-logo.png
  thumbnail: /images/openstack-logo.png
---

_Originally posted on [https://developer.ibm.com/opentech/2015/07/23/keystone-mid-cycle-recap-for-liberty/](https://developer.ibm.com/opentech/2015/07/23/keystone-mid-cycle-recap-for-liberty/)_

The Keystone mid-cycle for the Liberty release took place at Boston University (BU), and hosted by folks working on the [Massachusetts Open Cloud (MOC)](http://www.bu.edu/hic/research/massachusetts-open-cloud/). It was our most well attended mid-cycle, but still remained one of our most productive. Kudos to the folks at BU and the MOC for hosting us, and a special thanks goes out to Adam Young and Piyanai Saowarattitada for organizing most of the logistics.

## By the numbers

23 Keystone community members in attended, including 11 core reviewers. 9 different companies were represented, and from 5 different countries (USA, UK, Russia, Switzerland and Canada). We collaboratively revised and merged over 25 patches across the identity program's 5 repositories.

## Full list of attendees:

* [Geoff Arnold](https://twitter.com/geoffarnold) (Cisco)
* [Lance Bragstad](https://twitter.com/LanceBragstad) (Rackspace)
* [Lin Hua Cheng](https://twitter.com/linhuacheng) (Yahoo!)
* [Marek Denis](https://twitter.com/marekdenis) (CERN)
* [Morgan Fainberg](https://twitter.com/MdrnStm) (HP)
* Roxanna Gherle
* Robbie Harwood (Red Hat)
* David Hu (HP)
* [Brant Knudson](https://twitter.com/blknud) (IBM)
* [Anita Kuno](https://twitter.com/anteaya) (HP)
* Alexander Makarov (Mirantis)
* [Steve Martinelli](https://twitter.com/stevebot) (IBM)
* Angela Molock (Rackspace)
* [Henry Nash](https://twitter.com/henrynash) (IBM)
* Piyanai Saowarattitada (BU/MOC)
* Chang Lei Shi George Silvis (BU/MOC)
* [Davanum Srinivas](https://twitter.com/dims) (Mirantis)
* [David Stanek](https://twitter.com/dstanek) (Rackspace)
* [Brad Topol](https://twitter.com/bradtopol) (IBM)
* [Craig Tracey](https://twitter.com/craig_tracey) (Bluebox/IBM)
* [Guang Yee](https://twitter.com/gyeeeeee) (HP)
* [Adam Young](https://twitter.com/admiyoung) (Red Hat)

## Topics of interest:

### Dynamic Policy

Policies in OpenStack are currently stored in JSON files (typically named `policy.json`). Each project serves their own `policy.json`, and uses `oslo.policy` (read more about it [here](https://developer.ibm.com/opentech/2015/03/09/welcoming-oslo-policy-oslo-family/)) to evaluate the rules in the policy file. _The problems:_ Editing a file is a less-than-elegant UX, and different projects can define rules however they want, this leads to differing rules and policies. _The proposed solution:_ Centralize policies by storing them in SQL. A project would have the ability to fetch a policy, and get a newer one if needed, from Keystone. Samuel de Medeiros Queiroz (from UFCG) gave a fantastic demo of the solution over a video call. _My concerns:_ I'm not seeing enough desire from operators for this feature. The operators we had at the mid-cycle have simply learned to live with the issues. They use Ansible (or other automation tools) to update the policy files for each project on their hosts. Additionally, I think better examples and enforcement on how policy files are written would solve the issue of clashing rules. Overall, I'm hesitant to pick up a bunch of new code to solve an issue that folks have worked around. Adam gave a presentation on the problem and proposed solution, it's posted on his [github account](https://github.com/admiyo/keystone-rbac-presentation/blob/master/risk-assessment.pdf).

### keystoneauth

Morgan Fainberg gave an update on [keystoneauth](https://github.com/openstack/keystoneauth), an initiative to further break apart [python-keystoneclient](https://github.com/openstack/python-keystonclient). Once upon a time, `python-keystoneclient` was actually four projects in one, most folks just didn't know it at the time. I'll explain. [python-keystoneclient](https://github.com/openstack/python-keystoneclient) was providing:

* a middleware for services to include in their pipeline
* tools to create an authenticated session
* CRUD support for python bindings for our APIs
* a command line interface

When fully broken apart, the Identity team will offer the same features, just through different libraries:

* [keystonemiddleware](https://github.com/openstack/keystonemiddleware) will provide middleware for OpenStack projects
* [keystoneauth](https://github.com/openstack/keystoneauth) will be used to create authenticated sessions
* [python-keystoneclient](https://github.com/openstack/python-keystonclient) will be used for python bindings to Keystone APIs
* [OpenStackClient](https://github.com/openstack/python-openstackclient) will be providing a CLI for Keystone

### Functional Tests

I spent Thursday afternoon and most of Friday in a small working group. Together I hacked away on functional tests with Marek Denis, Roxanna Gherle, David Stanek and Anita Kuno. It has become increasingly clear that we need functional tests in Keystone, what was once an afterthought to most of us, is now a prime concern. We outlined 6 configurations that we need to start testing against:

1. _Our current CI/CD setup:_ SQL Identity, SQL Assignment, UUID Tokens
2. _Single LDAP for Identity:_ LDAP Identity, SQL Assignment, UUID and Fernet Token
3. _Multiple Identity Backends:_ SQL+LDAP Identity, SQL Assignment, UUID and Fernet Tokens
4. _Federating Identities:_ Federated Users + SQL Identity (service accounts), SQL Assignment, UUID and Fernet Token
5. _Keystone to Keystone:_ Any two of the above, with one setup as an IdP, the other as an SP.
6. _Notifications:_ Can reuse the current CI/CD, but requires a messaging service and listener to be setup.

## Bonus Points

The [Massachusetts Open Cloud](https://www.openstack.org/summit/openstack-summit-atlanta-2014/session-videos/presentation/the-massachusetts-open-cloud-moc-a-new-model-to-operate-and-innovate-in-a-vendor-neutral-cloud) gave a great live demo of their multi-federation setup. In their use case, users are coming in from a federated identity provider, and may use different service providers for different OpenStack services. For instance, they may use Service Provider A for compute resources (nova), but then use Service Provider B for volume (cinder). As a long time federation proponent, it was great to see folks using this in a way I didn't think possible. There were many, many other topics discussed: python 3.4 support, hierarchal multi-tenancy, reseller use cases, fernet tokens for federation, general code cleanup and refactoring, and role assignment improvements. For a full list of the nitty gritty details, look at the [etherpad](https://etherpad.openstack.org/p/keystone-liberty-midcycle-meetup).
