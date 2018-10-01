---
title: "Keystone Design Summit Outcome and Goals for Mitaka"
excerpt: "A recap from the Keystone design summit and goals for the next release, Mitaka"
tags: 
  - software
  - openstack
---

_Originally posted on [https://developer.ibm.com/opentech/2015/11/20/keystone-design-summit-outcome-and-goals-for-mitaka/](https://developer.ibm.com/opentech/2015/11/20/keystone-design-summit-outcome-and-goals-for-mitaka/)_

Better late than never! I took some off after the summit, but here's my blog about the Keystone Design Summit outcome and goals for Mitaka. Both [Boris Bobrov](https://www.mirantis.com/blog/openstack-keystone-in-tokyo-deprecations-deprecations-deprecations/) and [Dolph Mathews](http://dolphm.com/openstack-mitaka-design-summit-outcomes/) have already written fantastic recaps, I strongly recommend you take a look at those too. This blog is meant to summarize the action items from various design sessions, and should hopefully act as release notes in 6 months! This is by no means complete, but it's certainly close!

## To summarize the outcome in one sentence

_We'll be continuing the trend from our previous releases, focusing on performance, scalability, stability and adding just a few new features that are essential to both public and private clouds._

## Keystone Server

### Roles & Policy

Return names when listing role assignments **NEW API**
* Modify GET /v3/role_assignments to support &include_names flag
* This should improve usability for both Horizon and OpenStackClient
* Specification: [http://specs.openstack.org/openstack/keystone-specs/specs/mitaka/list-assignment-names.html](http://specs.openstack.org/openstack/keystone-specs/specs/mitaka/list-assignment-names.html)

Implied roles **NEW APIs**
* Have one role imply many roles
* Specification: [http://specs.openstack.org/openstack/keystone-specs/specs/backlog/implied-roles.html](http://specs.openstack.org/openstack/keystone-specs/specs/backlog/implied-roles.html)

Domain Scoped Roles **NEW APIs**
* Allow roles to be created within a domain, effectively allowing per-domain-policy
* Specification: [https://review.openstack.org/#/c/226661/](https://review.openstack.org/#/c/226661/)

Define Admin Project in config file
* Add the ability to define "this is my admin project"
* This should resolve a lot of the incorrect usage of the admin role being unnecessarily given to many projects (See [bug 968696](https://bugs.launchpad.net/keystone/+bug/968696))
* Specification: [http://specs.openstack.org/openstack/keystone-specs/specs/mitaka/is_admin_project.html](http://specs.openstack.org/openstack/keystone-specs/specs/mitaka/is_admin_project.html)

### Federation

Create shadow accounts for any user that has been created or authenticated (via local SQL, LDAP or federation)
* Greatly improve the story for federated users, we will be able to assign roles directly and trace their actions more easily for billing and auditing
* Specification: [https://review.openstack.org/#/c/240595/](https://review.openstack.org/#/c/240595/)

New specs that have APIs!
* Service provider and endpoint filtering, see specification: [https://review.openstack.org/188534](https://review.openstack.org/188534)
* Mark identity providers as public or private, see specification: [https://review.openstack.org/#/c/209941/](https://review.openstack.org/#/c/209941/)
* _Operator request_: Clean up logging, far too much in DEBUG and not enough in INFO

### Tokens

Continue to make Fernet tokens the go-to token format for Keystone
* DevStack and Tempest support is still being worked on, needs to be completed before Keystone can make it the default format
* Improve documentation, lots of unofficial docs via blog posts are causing misconceptions

### Catalog

Retrieve the service catalog with an unauthenticated call
* Part of a larger cross-project effort, we will be looking to return a well defined service catalog on a new API
* This will allow for a better service-discovery story for OpenStack as a whole
* Implementation and API has yet to be finalized

### Fixing broken things

Pagination
* Pagination for projects, roles and doamins, is possible now since they are only available in SQL
* Pagination for users and groups in LDAP? We can add support for it, but YMMV

Custom TTL on tokens for long-running operations
* Make keystone_authtoken configurable with a custom TTL, token validation uses this value or ignores the expires_at

REST interface for domain config **NEW API**
* List default values of a domain config, see specification: [https://review.openstack.org/#/c/185650/](https://review.openstack.org/#/c/185650/)

### Extensions

OS-FEDERATION, OS-ENDPOINT-POLICY, OS-REVOKE, OS-OAUTH1, OS-INHERIT, OS-EP-FILTER will be moved to keystone proper
* The old routers and SQL backends will be deprecated in M and be removed in O
* Paste files will need to be updated to point to the new resources before O is released No more extensions - ever.

### Deprecations

Each of these items will follow the [standard deprecation policy](http://governance.openstack.org/reference/tags/assert_follows-standard-deprecation.html) that the TC has now publicized.

v2.0 of the Identity API **Deprecate in M, remove in O or greater**
* We will maintain some v2.0 authentication calls, such as: POST /v2.0/tokens and GET /v2.0/tenants

PKI token format **Deprecate in M, remove in O**
* Contains a major security bug
* If PKI format is specified, the resultant token will be a UUID token

LDAP write support **Deprecate in M, remove in O**
* Rarely do OpenStack deployers want to write to LDAP, and more rarely do LDAP administrators want to allow this sort of operation

### Removals

Eventlet **Deprecated in K, to be removed in M**
* May live to see another release, need confirmation from mailing list
* LDAP as a resource backend, to store projects, domains and roles **Deprecated in K, to be removed in M**

## Keystone Libraries

### keystoneauth

We need to support federation protocol like SAML and kerberos
* Since support for these pulls in additional libraries, they will be 'optional'
* Install these optional plugins with: pip install keystoneauth[kerberos] or pip install keystoneauth[saml]
* The python-keystoneauth-saml repo will be removed (there were no releases of it)
* The python-keystoneclient-kerberos repo will become inactive and eventually removed (there were 3 minor releases)
* Improve the documentation: Show how to create plugins for the federation plugins, and also explain k2k flows

### keystonemiddleware

* Adapt keystonemiddleware to use keystoneauth
* Tokenless auth support
* Deprecate certain auth_token configuration values

### keystoneclient

* Only changes to the CRUD calls should be added or modified
* Authentication plugins should go into keystoneauth, and CLI should go into openstackclient
* Modify other python-*clients to use keystoneauth
* Deprecate auth plugins and session code (remove these in O)
* Potentially remove CLI and mark keystoneclient as 2.0 (need to check deprecation policy for clients)
* Potentially remove middleware and mark keystoneclient as 3.0 (need to check deprecation policy for clients)

