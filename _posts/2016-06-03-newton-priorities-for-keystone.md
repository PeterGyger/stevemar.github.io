---
title: "Newton priorities for Keystone"
excerpt: "A rundown of what will be doing on in the New priorities for Keystone"
tags:
  - software
  - openstack
image:
  path: /images/generic/openstack-logo.png
  thumbnail: /images/generic/openstack-logo.png
---

_Originally posted on [https://developer.ibm.com/opentech/2016/06/03/newton-priorities-for-keystone](https://developer.ibm.com/opentech/2016/06/03/newton-priorities-for-keystone)_

Below is a list of priorities the Keystone team has indicated are priorities for the Newton development cycle. As the Keystone Project Team Lead (PTL) for the Newton development cycle, I plan on trying to hit as many of these goals as possible.

Other folks have written up summit recaps and goals in their own blogs:

* Colleen Murphy: [http://www.gazlene.net/austin-summit.html](http://www.gazlene.net/austin-summit.html)
* Adam Young: [http://adam.younglogic.com/2016/05/identity-newton](http://adam.younglogic.com/2016/05/identity-newton)
* Dolph Mathews: [http://dolphm.com/openstack-newton-design-summit-outcomes-for-keystone](http://dolphm.com/openstack-newton-design-summit-outcomes-for-keystone)

## [Configurable password compliance](http://specs.openstack.org/openstack/keystone-specs/specs/keystone/newton/pci-dss.html)

This will allow operators to have password requirements in a configurable manner. Change factors such as minimum password character length, number of days a user has before the password must be changed, number of allowed failed attempts, etc.

## [Python 3.X](http://specs.openstack.org/openstack/keystone-specs/specs/keystone/ongoing/python3.html)

We are close to having full python3 support for Keystone. The main issue holding us back is python3 support for python-ldap and ldappool, the two main libraries we use for our LDAP support. We have a plan: switch python-ldap to pyldap (a py3 port) and contact the ldappool owner to propose maintaining the project and making it py3 friendly.

## [Online schema migrations](http://specs.openstack.org/openstack/keystone-specs/specs/keystone/backlog/online-schema-migration.html)

Commit to only additive schema migrations (new tables or new columns) going forward; Subtractive schema migrations (deleting or renaming tables and columns) are to go through a deprecation period, and we write data to both locations. Nova already does this, take a look at their [developer docs](http://docs.openstack.org/developer/nova/upgrade.html#migration-policy) for more info.

## Fernet tokens as default

Self explanatory. Operators provided positive feedback on their experiences with Fernet tokens, and would support having them as the default token format. This will impact documentation for both new installs and upgrades, since additional steps will be required.

## Migrate everything from keystoneclient.auth to keystoneauth

novaclient and neutronclient were the first to migrate over in the Mitaka release. Ideally, all server and client projects should be using keystoneauth as the preeminent way of authenticating with keystone. As a secondary to do, OpenStackClient will release a 3.0.0 version which, amongst other major changes, includes a move to keystoneauth. This will be a huge benefit for folks that are eager to use the CLI for federated identity operations.

## [Improving federated identity](https://review.openstack.org/#/c/324055)

Currently, we are not quite meeting the needs of operators with respect to bootstrapping new users, role assignments and “personal projects”. Better UX is needed in general, it's still too complex to setup. Possibly handle SAML assertions directly instead of using an apache module, i.e. leverage pysaml library. Making automation easier, since it’ll just be REST calls.

## Major deprecations and removals

* **Deprecating** ADMIN_TOKEN, in favor of keystone-manage bootstrap
* **Deprecating** keystonemiddleware’s s3 module, it will be moved under the swift umbrella
* **Removing** support for running keystone under eventlet, in favor of running keystone in a web server
* **Removing** keystoneclient’s CLI, in favor of using OpenStackClient
