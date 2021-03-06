---
title: "Welcoming oslo.policy into the OpenStack Oslo family"
excerpt: "Where we officially announce oslo.policy graduating from the incubator"
tags:
  - software
  - openstack
---

_Originally posted on [https://developer.ibm.com/opentech/2015/03/09/welcoming-oslo-policy-oslo-family](https://developer.ibm.com/opentech/2015/03/09/welcoming-oslo-policy-oslo-family)_

Welcome OpenStack Oslo's newest addition to the family, oslo.policy! Last week the newest addition to the [Oslo](https://wiki.openstack.org/Oslo) program was [oslo.policy](https://pypi.python.org/pypi/oslo.policy), which officially graduates the policy related code from [oslo-incubator](https://github.com/openstack/oslo-incubator/) to [its own library](https://github.com/openstack/oslo.policy). For readers wondering what Oslo is, let's take the time to briefly explain the program. Oslo is defined as the OpenStack Common Libraries program, it’s mission statement provides an excellent description.

> To produce a set of python libraries containing code shared by OpenStack projects. The APIs provided by these libraries should be high quality, stable, consistent, documented and generally applicable.

Any new additions to the Oslo API must go through a period of incubation, where the code is hosted in oslo-incubator. Other OpenStack projects may leverage the incubated code, and eventually the code may graduate to it's own library. To date, Oslo has graduated 13 libraries. These include libraries ranging from providing common logging standards (oslo.log), to database connection helpers (oslo.db), and also utilities for working with internationalization and translations (oslo.i18n).

The newest library being released is oslo.policy, which I had the pleasure of helping prepare for graduation. The policy code is used by nearly all OpenStack projects, as it evaluates whether a user has the authorization to perform the requested action, based on the credentials that were passed to it. The main driving factor behind the work was to have the security sensitive policy code managed in a library. Should a security related defect arise, releasing a new version of a library is much less trouble than syncing each project that was consuming code from oslo-incubator.

A note about the graduation process, it was my first time participating in the process and it was an absolute pleasure! Doug Hellman (Oslo PTL) has [outlined](https://wiki.openstack.org/wiki/Oslo/CreatingANewLibrary) all the steps required to turn incubator code into a library that stands on its own. As discussed at the beginning of Hellman's article, the graduation process is long due to the number of steps involved. Also, by participating in the graduation process, a developer will have a chance to interact with other OpenStack projects (infrastructure, devstack, etc) that they might not normally interact with, so in that regard it’s a great learning experience.

Graduating incubated code into standalone libraries, and following the process is not flashy or glamorous work. But it’s the type of work that is appreciated by developers of the OpenStack community and you will receive lots of kudos for doing this work. There is still plenty of code in oslo-incubator that needs to be graduated and I strongly encourage anyone who's up for a challenge to take a look!
