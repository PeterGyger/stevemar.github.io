---
title: "Leveraging OpenStackClient as your unified command line interface"
excerpt: "OpenStackClient (OSC) becomes an official project and releases v1.0.3"
tags: 
  - software
  - openstack
image:
  path: /images/openstackcli.png
  thumbnail: /images/openstackcli.png
---

_Originally posted on [https://developer.ibm.com/opentech/2015/03/20/leveraging-openstackclient-unified-command-line-interface](https://developer.ibm.com/opentech/2015/03/20/leveraging-openstackclient-unified-command-line-interface)_

It's fitting that I publish this write up at this current time. [OpenStackClient v1.0.3](https://pypi.python.org/pypi/python-openstackclient/) was released last week and this release includes a large number of feature requests and bug fixes. But the even more exciting news is that the OpenStack Technical Committee voted in favor to include OpenStackClient as part of it's list of official projects! The newly integrated OpenStackClient project will include [python-openstackclient](https://github.com/openstack/python-openstackclient), [cliff](https://github.com/openstack/cliff) and [os-client-config](https://github.com/stackforge/os-client-config). This news was especially nice to hear, since I've been involved with the project for [quite a while](https://review.openstack.org/#/c/19999/). For readers wondering what OpenStackClient (OSC) is, it’s mission statement provides an excellent description.

> To provide a single command line interface for OpenStack services with a uniform command set and format.

## Overview of current command line tools

Each OpenStack program has their own client that has python bindings; think of these as regular python libraries. The Compute (Nova) program has _python-novaclient_, Object Store (Swift) has _python-swiftclient_, and Identity (Keystone) has _python-keystoneclient_. Unfortunately, each client also delivers a built-in command line interface. This leads to a very splintered and inconsistent user experience. Let’s look at the following examples:

```bash
$ nova list
$ nova flavor-list
$ keystone list
$ keystone user-list
$ swift list
$ swift list Pictures
```

With the exception of _keystone list_, all the above commands work, but sometimes a _list_ argument is all that is needed, other times it's _resource_-list (flavor-list/user-list) or an actual resource name (Pictures). Cases where an add or remove action is performed are just as strange. Sometimes they are treated as optional arguments, other times as positional.

```bash
$ keystone user-role-remove --user user_x --role admin --tenant production
$ neutron dhcp-agent-network-remove dhcp_agent network
```

These types of inconsistencies (and many more) and their negative impact on user experience are what triggered the need for a common command line interface.

## OpenStackClient, the CLI that will solve all your problems

**_Predictable command format_** One of OpenStackClient's main goal is to be predictable and easy to understand. Here are the basics of an _openstack_ command:

```bash
openstack (object-1) (action) [(object-2)]
```

It can be expressed in English as "(Take) object-1 (and perform) action (using) object-2 (to it)." For example, adding a user to a group, "Take group (admins) and perform add using user (alice) to it.":

```bash
$ openstack group add user admins alice
```

For a full list of supported commands, check out our [docs](http://docs.openstack.org/developer/python-openstackclient/commands.html). **_Authenticating_** OpenStackClient leverages _python-keystoneclient_'s authentication plugins; so users can authenticate with a token, password, trust or even using a SAML assertion. Authentication can be performed with environment variables or passed in as command arguments. The environment variable names and command argument names will be very similar (_--os-username_ vs _OS_USERNAME_). To authenticate with OpenStack Identity Service's version 3.0 API, try using the following:

```bash
$ export OS_IDENTITY_API_VERSION=3
$ export OS_AUTH_URL=http://localhost:5000/v3
$ export OS_DEFAULT_DOMAIN=default
$ export OS_USERNAME=admin
$ export OS_PASSWORD=openstack
$ export OS_PROJECT_NAME=admin
```

**_Example Identity v3 commands_** One of the driving factors for OpenStackClient is support for version 3 commands of OpenStack's Identity service. Below are some examples that showcase OpenStackClient working with Identity Service v3 only concepts.

#### Creating a new group

```bash
$ openstack group create my_test_group
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description |                                  |
| domain_id   | default                          |
| id          | 4cec99eb65464875a968497784fec02f |
| name        | my_test_group                    |
+-------------+----------------------------------+
```

#### Listing domains

```bash
$ openstack domain list
+---------+---------+---------+----------------------------------------------------------------------+
| ID      | Name    | Enabled | Description                                                          |
+---------+---------+---------+----------------------------------------------------------------------+
| default | Default | True    | Owns users and tenants (i.e. projects) available on Identity API v2\. |
+---------+---------+---------+----------------------------------------------------------------------+
```

**_Looking ahead_** The OpenStackClient team has several cool ideas that have yet to be implemented. Such as user-based configs (so we no longer depend on environment variables or command options), caching tokens to reduce trips to keystone, and maybe even a smarter engine that reduces the amount of interaction required. There is no reason that the following commands:

```bash
$ nova boot --flavor='2G' -- image='Gentoo' # Nova talks to Glance
$ cinder give-me-a-10G-volume
$ nova attach-that-volume-to-my-computer # nova talks to cinder
$ neutron give-me-an-ip
$ nova attach-that-floating-ip-to-my-computer # nova talks to neutron
$ designate call-that-ip 'example.com' --reverse-dns # designate to neutron
```

Can't be consolidated to:

```bash
$ openstack boot gentoo on-a 2G-VM with-a publicIP with-a 10G-volume call-it example.com
```
