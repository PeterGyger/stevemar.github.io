---
title: "Configuring Keystone with IBM’s Bluepages LDAP"
excerpt: "Learn how to integrate your corporate LDAP identity provider with Keystone"
tags:
  - software
  - openstack
image:
  path: /images/generic/openstack-arch.svg
  thumbnail: /images/generic/openstack-arch.svg
---

_Originally posted on [https://developer.ibm.com/opentech/2015/08/14/configuring-keystone-with-ibms-bluepages-ldap/](https://developer.ibm.com/opentech/2015/08/14/configuring-keystone-with-ibms-bluepages-ldap/)_

In this post I'll be configuring Keystone to have an LDAP identity backend. In this case, I'll be using Bluepages, which is IBM's internal LDAP. The majority of the steps should be the same for most organizations, a few LDAP specific configuration values will have to change. The post is divided up into four logical portions: 1) Setting up Keystone for LDAP, 2) Admin operations on LDAP, 3) Authenticating as an LDAP user, and 4) Logging in with Horizon.

## Updates: F.A.Q.'s

* _Updated 8/31/2016_: Since initially writing this article, I've had a bunch of questions. Here are some:

* _Q: How can I speed up the authentication process?_
* A: Use LDAP connection pools, it's documented in [Keystone's docs](http://docs.openstack.org/developer/keystone/configuration.html#connection-pooling), and [Matt Fischer](https://twitter.com/openmfisch) has [blogged about](http://www.mattfischer.com/blog/?p=624) it too, even the default settings will speed things up.

* _Q: Why are new instances are not getting a floating IP?_
* A: In Horizon, use the **Allocate IP To Project** button on the **Floating IPs** tab of the **Access & Security** page. Through the CLI, use `nova floating-ip-create`

## Setting up Keystone for LDAP

To start, I launch [DevStack](https://github.com/openstack-dev/devstack/) to install the latest master version of Keystone and other OpenStack services. I do this simply because DevStack sets up the service accounts, endpoints and services for me. Though I'm using the master branch of OpenStack (and DevStack), this guide should work if you have a Kilo or Juno version of OpenStack, too. I should also mention that I am using [OpenStackClient 1.6.0](https://pypi.python.org/pypi/python-openstackclient/1.6.0). My `local.conf` config file for DevStack is pretty simple, just the essential services and some passwords:

```ini
RECLONE=yes
ENABLED_SERVICES=key,g-api,g-reg,n-api,n-crt,n-obj,n-cpu,n-net,n-cond,cinder,c-sch,c-api,c-vol,n-sch,n-cauth,horizon,mysql,rabbit
SERVICE_TOKEN=openstack
ADMIN_PASSWORD=openstack
MYSQL_PASSWORD=openstack
RABBIT_PASSWORD=openstack
SERVICE_PASSWORD=openstack
LOGFILE=/opt/stack/logs/stack.sh.log
```

Once DevStack completes, to use the LDAP functionality of Keystone we have to install a few extra python libraries. These are not installed by default:

```bash
$ sudo pip install python-ldap
$ sudo pip install ldappool
```

Now it's time to configure our environment variables to work with version 3 of the Identity API and create LDAP specific entries:

```bash
$ env | grep OS
OS_IDENTITY_API_VERSION=3
OS_PASSWORD=openstack
OS_AUTH_URL=http://172.16.240.135:5000/v3
OS_USERNAME=admin
OS_PROJECT_NAME=admin
OS_USER_DOMAIN_NAME=Default
OS_PROJECT_DOMAIN_NAME=Default
```

### Background on the architecture

_Service accounts_ (like the admin account, or nova and glance accounts) must exist for services to authenticate via [keystonemiddleware](https://github.com/openstack/keystonemiddleware). _User accounts_ will be stored in an LDAP. In most enterprise environments, adding _Service accounts_ is probably not advised. To resolve this issue, deployers are recommended to store _Service accounts_ in the Default domain, and create another domain for _User accounts_. This creates a logical division between the two identity sources. Furthermore, each domain can have it's own identity source and be backed by either SQL or LDAP. In this post, we will be storing the _Service accounts_ in SQL and the _User accounts_ in LDAP. I've tried to display this in the image below:

[![Screen Shot 2015-08-14 at 1.46.38 AM](http://developer.ibm.com/opentech/wp-content/uploads/sites/43/2015/08/Screen-Shot-2015-08-14-at-1.46.38-AM.png)](http://developer.ibm.com/opentech/wp-content/uploads/sites/43/2015/08/Screen-Shot-2015-08-14-at-1.46.38-AM.png)

As a reminder, the _Identity_ backend handles `Users` and `Groups`, the _Resource_ backend handles `Domains` and `Projects`, and lastly, the _Assignment_ backend handles `Roles`.

### Keystone setup continued

By default, DevStack creates _Service accounts_ in the Default domain (backed by SQL). We simply need to create a new domain for our _User accounts_ (backed by LDAP). In the block below we'll create domain `ibm` and a project `ibmcloud` within the domain.

```bash
$ openstack domain create ibm
+---------+----------------------------------+
| Field   | Value                            |
+---------+----------------------------------+
| enabled | True                             |
| id      | 421370cd78234413bbeb5d7ce1c73077 |
| name    | ibm                              |
+---------+----------------------------------+

$ openstack project create ibmcloud --domain ibm
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description |                                  |
| domain_id   | 421370cd78234413bbeb5d7ce1c73077 |
| enabled     | True                             |
| id          | 95ae2a9c259b4012b8b7e8ad7dc9a939 |
| name        | ibmcloud                         |
| parent_id   | None                             |
+-------------+----------------------------------+
```

Next up, we need to update `keystone.conf`, to enable domain specific identity drivers:

```ini
[identity]
domain_specific_drivers_enabled = True
domain_config_dir = /etc/keystone/domains
```

The `domain_config_dir` specifies where to house configuration files that are specific to a domain. They **must** be named in the following manner: `keystone.DOMAIN_NAME.conf`. Create `/etc/keystone/domains/keystone.ibm.conf`, and populate it with a whole bunch of LDAP specific values (seen below). If you are having trouble finding these values for your LDAP, use tools such as: [ldapsearch](http://linux.die.net/man/1/ldapsearch) or [jxplorer](http://jxplorer.org/):

```ini
[identity]
driver = ldap

[ldap]
url = ldap://bluepages.ibm.com
suffix = "ou=bluepages,o=ibm.com"
query_scope = sub

user_tree_dn = "ou=bluepages,o=ibm.com"
user_objectclass = ibmPerson
user_id_attribute = uid
user_name_attribute = mail
user_mail_attribute = mail
user_pass_attribute = userPassword
user_enabled_attribute = enabled

group_tree_dn = "ou=memberlist,ou=ibmgroups,o=ibm.com"
group_objectclass = groupOfUniqueNames
group_id_attribute = cn
group_name_attribute = cn
group_member_attribute = uniquemember
group_desc_attribute = description
```

Note, if using an older version of Keystone, then the full driver name may need to be specified, like this:

```ini
[identity]
driver = keystone.identity.backends.ldap.Identity
```

The last step is to restart Keystone

```bash
$ sudo service apache2 restart
```

## Admin operations on LDAP

With the same credentials we used previously, let's try a few administrative tasks to ensure your configuration is working. Let's try: 1) finding a user, 2) finding groups the user is a member of, 3) finding a specific group, 4) finding all users of a group, and 5) assigning a role to a group:

```bash
# 1. Find a user
$ openstack user show stevemar@ca.ibm.com --domain ibm
+-----------+------------------------------------------------------------------+
| Field     | Value                                                            |
+-----------+------------------------------------------------------------------+
| domain_id | c3c24e60d57e4461ad64b372c14128b7                                 |
| email     | stevemar@ca.ibm.com                                              |
| id        | c29cac8f7003e6de36a47b85f306f137bea8026e3d55c35aed27dfbf09c8fb28 |
| name      | stevemar@ca.ibm.com                                              |
+-----------+------------------------------------------------------------------+

# 2. Find groups the user is a member of
$ openstack group list --user stevemar@ca.ibm.com --user-domain ibm
+------------------------------------------------------------------+--------------------------------------+
| ID                                                               | Name                                 |
+------------------------------------------------------------------+--------------------------------------+
| 178e5df37393dd6695c4d93382c3fe46a048f60b21b393373ff8360a191b74bb | SWG_Canada                           |
| 5f620ab5abe2f0b14e3112357dd81069a03ca3ac637e7416b7d562152f73e938 | Toronto_Lab_VPN                      |
| ed9f9cb5dbefd6a6e5808ca97921734da3b90b5d160e0a7308c199680c595a96 | HiPODS-OpenCloud                     |

# 3. Find a specific group
$ openstack group show SWG_Canada --domain ibm
+-----------+------------------------------------------------------------------+
| Field     | Value                                                            |
+-----------+------------------------------------------------------------------+
| domain_id | c3c24e60d57e4461ad64b372c14128b7                                 |
| id        | ed9f9cb5dbefd6a6e5808ca97921734da3b90b5d160e0a7308c199680c595a96 |
| name      | HiPODS-OpenCloud                                                 |
+-----------+------------------------------------------------------------------+

# 4. Find all the users of a group
$ openstack user list --group ed9f9cb5dbefd6a6e5808ca97921734da3b90b5d160e0a7308c199680c595a96
+------------------------------------------------------------------+--------------------------------+
| ID                                                               | Name                           |
+------------------------------------------------------------------+--------------------------------+
| c29cac8f7003e6de36a47b85f306f137bea8026e3d55c35aed27dfbf09c8fb28 | stevemar@ca.ibm.com            |
| f3ca1de06fbe100e71577aad68ccd588f9d792686ae75812becfe43d5b4aa09c | topol@us.ibm.com               |

# Note, for the previous command I've submitted a patch to reference groups by domain name.
# The following will work in the next release of openstackclient:
# $ openstack user list --group "HiPODS-OpenCloud" --domain ibm

# Assign a role of 'member' to users from the HiPODS group, to access the project 'ibmcloud'
$ openstack role add member --group "HiPODS-OpenCloud" --group-domain ibm --project ibmcloud --project-domain ibm
```

Note, if these list operations are not properly filtered, you will probably see an exception is raised. This is because Keystone will attempt to list ALL users or ALL groups, the LDAP connection will simply time out.

## Authenticating as an LDAP user

Now to double check that everything works from a user's perspective. Before we issue commands, open a new terminal and change your environment variables. The main difference in these new values (aside from the obvious change in username and password) are the addition of `OS_USER_DOMAIN_NAME` and `OS_PROJECT_DOMAIN_NAME` which are set to `ibm`:

```bash
$ env | grep OS
OS_IDENTITY_API_VERSION=3
OS_PASSWORD=My5uper5ecretPa$word
OS_AUTH_URL=http://172.16.240.134:5000/v3
OS_USERNAME=stevemar@ca.ibm.com
OS_USER_DOMAIN_NAME=ibm
OS_PROJECT_NAME=ibmcloud
OS_PROJECT_DOMAIN_NAME=ibm
```

Let's try a few operations: 1) getting a token, 2) listing images, 3) listing flavors, and 4) creating a new VM:

```bash
# 1. Get a token:
$ openstack token issue
+------------+------------------------------------------------------------------+
| Field      | Value                                                            |
+------------+------------------------------------------------------------------+
| expires    | 2015-08-13T21:54:15.411376Z                                      |
| id         | 7c0df68c6b734735a22b2365f241b9b8                                 |
| project_id | e68bd223fb8045f6b7b2b7aa926433c8                                 |
| user_id    | c29cac8f7003e6de36a47b85f306f137bea8026e3d55c35aed27dfbf09c8fb28 |
+------------+------------------------------------------------------------------+

# 2. List images
$ openstack image list
+--------------------------------------+---------------------------------+
| ID                                   | Name                            |
+--------------------------------------+---------------------------------+
| 1f8a0809-5aa5-454a-916b-6077c00e20e1 | cirros-0.3.4-x86_64-uec         |
| 1d181a59-7ab7-4b5c-a351-118383d0b58b | cirros-0.3.4-x86_64-uec-ramdisk |
| b8aa23ee-1791-4a0e-b42a-cac6f7607b21 | cirros-0.3.4-x86_64-uec-kernel  |
+--------------------------------------+---------------------------------+

# 3. List flavors
$ openstack flavor list
+----+-----------+-------+------+-----------+-------+-----------+
| ID | Name      |   RAM | Disk | Ephemeral | VCPUs | Is Public |
+----+-----------+-------+------+-----------+-------+-----------+
| 1  | m1.tiny   |   512 |    1 |         0 |     1 | True      |
| 2  | m1.small  |  2048 |   20 |         0 |     1 | True      |
| 3  | m1.medium |  4096 |   40 |         0 |     2 | True      |
| 4  | m1.large  |  8192 |   80 |         0 |     4 | True      |
| 5  | m1.xlarge | 16384 |  160 |         0 |     8 | True      |
+----+-----------+-------+------+-----------+-------+-----------+

# 4. Launch a VM
$ openstack server create myVM --image cirros-0.3.4-x86_64-uec --flavor 1
+--------------------------------------+------------------------------------------------------------------+
| Field                                | Value                                                            |
+--------------------------------------+------------------------------------------------------------------+
| OS-DCF:diskConfig                    | MANUAL                                                           |
| OS-EXT-AZ:availability_zone          | nova                                                             |
| OS-EXT-STS:power_state               | 0                                                                |
| OS-EXT-STS:task_state                | scheduling                                                       |
| OS-EXT-STS:vm_state                  | building                                                         |
| OS-SRV-USG:launched_at               | None                                                             |
| OS-SRV-USG:terminated_at             | None                                                             |
| accessIPv4                           |                                                                  |
| accessIPv6                           |                                                                  |
| addresses                            |                                                                  |
| adminPass                            | PQDNiboGaNQ2                                                     |
| config_drive                         |                                                                  |
| created                              | 2015-08-14T07:35:36Z                                             |
| flavor                               | m1.tiny (1)                                                      |
| hostId                               |                                                                  |
| id                                   | fe2b4ab0-80cf-48fe-ad5b-3e8b9624edd1                             |
| image                                | cirros-0.3.4-x86_64-uec (1f8a0809-5aa5-454a-916b-6077c00e20e1)   |
| key_name                             | None                                                             |
| name                                 | myVM                                                             |
| os-extended-volumes:volumes_attached | []                                                               |
| progress                             | 0                                                                |
| project_id                           | e68bd223fb8045f6b7b2b7aa926433c8                                 |
| properties                           |                                                                  |
| security_groups                      | [{u'name': u'default'}]                                          |
| status                               | BUILD                                                            |
| updated                              | 2015-08-14T07:35:36Z                                             |
| user_id                              | c29cac8f7003e6de36a47b85f306f137bea8026e3d55c35aed27dfbf09c8fb28 |
+--------------------------------------+------------------------------------------------------------------+
```

## Logging in with Horizon

For the last portion of this post, I'll briefly show the changes necessary to Horizon to allow _User accounts_ to authenticate. Make the following changes to `horizon/openstack_dashboard/local/local_settings.py`:

```ini
OPENSTACK_API_VERSIONS = {
    "data-processing": 1.1,
    "identity": 3,
    "volume": 2,
}
OPENSTACK_KEYSTONE_MULTIDOMAIN_SUPPORT = True
OPENSTACK_KEYSTONE_URL="http://172.16.240.134:5000/v3"
```

Restart Horizon one last time...

```bash
$ sudo service apache2 restart
```

When refreshed, the landing page should now include a new field to log in with a domain of your choice and your LDAP credentials. Once logged in, your email should appear on the top right - Ta Da!

> **Landing page with new domain name option**

[![Screen Shot 2015-08-13 at 9.09.56 PM](http://developer.ibm.com/opentech/wp-content/uploads/sites/43/2015/08/Screen-Shot-2015-08-13-at-9.09.56-PM.png)](http://developer.ibm.com/opentech/wp-content/uploads/sites/43/2015/08/Screen-Shot-2015-08-13-at-9.09.56-PM.png)

> **Once logged in, IBM email should identify the user at the top-right**

[![Screen Shot 2015-08-13 at 9.09.17 PM](http://developer.ibm.com/opentech/wp-content/uploads/sites/43/2015/08/Screen-Shot-2015-08-13-at-9.09.17-PM.png)](http://developer.ibm.com/opentech/wp-content/uploads/sites/43/2015/08/Screen-Shot-2015-08-13-at-9.09.17-PM.png)

## References

* [Keystone Docs](http://docs.openstack.org/developer/keystone/configuration.html#domain-specific-drivers)
* [Henry Nash's Developer Works Article](http://www.ibm.com/developerworks/cloud/library/cl-configure-keystone-ldap-and-active-directory/index.html)
