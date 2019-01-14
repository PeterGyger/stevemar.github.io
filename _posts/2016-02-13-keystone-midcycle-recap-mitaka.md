---
title: "Keystone mid-cycle recap for Mitaka"
excerpt: "A recap of topics that were discussed at the Mitaka mid-cycle meetup"
tags: 
  - software
  - ibm
  - openstack
  - recap
image:
  path: /images/openstack-logo.png
  thumbnail: /images/openstack-logo.png
---

_Originally posted on [https://developer.ibm.com/opentech/2016/02/13/keystone-mid-cycle-recap-for-mitaka/](https://developer.ibm.com/opentech/2016/02/13/keystone-mid-cycle-recap-for-mitaka/)_

The keystone mid-cycle for the Mitaka release took place at the IBM Austin Lab site, special thanks goes out to my management team ([Vince Brunssen](https://twitter.com/vbrunssen) and [Todd Moore](https://twitter.com/tmmoore_1)) for helping me organize and secure an awesome space. It was a fast-paced three day event that from brought operators, consumers, active contributors, and core developers together to focus on the keystone project. There was plenty of time to code and create patches for bugs and blueprints, but we also had a fair bit of discussion and direction for the project; we're hoping this makes our design sessions at the Austin summit a bit more focused than usual.

# Quick Summary

Over 25 keystone community members attended, including 8 core reviewers. 6 different companies were represented. We collaboratively revised and merged over 20 patches across the identity program’s five main repositories (_keystone, keystoneauth, python-keystoneclient, keystone-specs and keystonemiddleware_). We **nominated two new core members**: [Samuel de Medeiros Queiroz](https://twitter.com/samuel_dmq) (HP) and Dave Chen (Intel)!! Huge congratulations go out to them, very well deserved! We even managed to use the phone and audio systems available so we could have our necessary dose of [Adam Young](https://twitter.com/admiyoung), who was unable to physically join us this time around. We picked up a lot of momentum at the mid-cycle, and it's carrying us forward to the Mitaka finish line! For a full breakdown of what went on, we took notes on this [etherpad](https://etherpad.openstack.org/p/keystone-mitaka-midcycle).

# High Level Summary

## Format

We changed the format of the mid-cycle a bit this time around. In the mornings we worked as a large group to discuss project direction and hash out designs of new features, we did this so the Newton design summit is a bit more focused and productive than it has historically been. In the afternoons, we focused on the more traditional mid-cycle approach - code, code, code! The change of format was an experimental and I received a lot of positive feedback regarding it, hopefully we can adopt this format again in the future.

## TOTP

We granted a feature freeze exemption for the Time-Based One-Time Password (TOTP) specification. TOTP creates a stepping stone for making Multi-Factor Authentication (MFA) available in the Newton release. Details about the spec can be found [here](http://specs.openstack.org/openstack/keystone-specs/specs/mitaka/totp-auth.html). At the mid-cycle, we collaboratively fine-tuned the spec and approved it with the condition that a patch be made available through Gerrit in one week.

## Stabilization Cycle

The possibility of having a [stabilization cycle](http://lists.openstack.org/pipermail/openstack-dev/2016-January/084564.html), where we focus on bugs and technical debt instead of features was discussed. Turns out most of the keystone core team is against this approach, we decided to not have a stabilization cycle during the Newton development cycle.

## Keystone Q&A / Ask the experts

For the first time at a mid-cycle, we had a one hour block to allow some of the attendees to ask questions about keystone. We’ve noticed that as more folks attend the mid-cycle, there are a fair number of operators, users, and consumers, who can provide very valuable feedback on the project. This allowed folks (rookies or seasoned vets) to ask questions to the keystone core team in a comfortable setting. It was generally well received, when the session ended, everyone felt like they learned a bit more about keystone.

## Compliance for SQL identity backends

This topic was brought up by folks at Rackspace and IBM. There is desire to have users that are stored in an SQL backend (i.e. not users coming from LDAP or a federated identity provider) to be either [PCI DSS](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act) or [HIPAA](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) compliant. It was agreed that this would be a great feature to have, we worked on the spec a bit. But look for the functionality to be included early on in the Newton release.

## Eventlet/Apache/Uwsgi

Keystone can be deployed in many ways. The first method way to deploy keystone was to use eventlet, there's a fair bit of custom code in the keystone project to stand up keystone with eventlet, it's by far the most simple. We currently recommended using Apache and mod_wsgi, this combination is tested by default when a new patch is proposed to any OpenStack project. We'd like to remove eventlet, we have had it deprecated for two releases (since Kilo). We'd like to remove it because there have been a few eventlet specific bugs, performance with Apache or another HTTP server is much better, and more there are additional libraries for federated identity that are only available to Apache and other HTTP servers (which keystone uses for it's federated identity capabilities). Before we remove eventlet we'd like to prove to the operators that keystone can run in a variety of HTTP servers, look for devstack patches that set up keystone using uwsgi and gunicorn.
