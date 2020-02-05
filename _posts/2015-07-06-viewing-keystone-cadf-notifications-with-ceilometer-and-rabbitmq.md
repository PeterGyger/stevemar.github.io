---
title: "Viewing Keystone CADF notifications with Ceilometer and RabbitMQ"
excerpt: "Taking a hack at making sure Keystone and Ceilometer can talk CADF lingo"
tags:
  - software
  - openstack
image:
  path: /images/generic/openstack-arch.svg
  thumbnail: /images/generic/openstack-arch.svg
---

_Originally posted on [https://developer.ibm.com/opentech/2015/07/06/viewing-keystone-cadf-notifications-with-ceilometer-and-rabbitmq/](https://developer.ibm.com/opentech/2015/07/06/viewing-keystone-cadf-notifications-with-ceilometer-and-rabbitmq/)_

This post is long overdue, but better late than never! This post will show how a user can view Keystone CADF notifications in both Ceilometer and RabbitMQ. To start, let's go over some basic concepts first.

## Definitions

* **Events**: An event is triggered when a user accesses certain APIs (i.e.: an administrator creates a new user)
* **Auditing**: We want to be able to figure out who did what, from where, and to what resource.
* [**Ceilometer**](http://docs.openstack.org/developer/ceilometer/): OpenStack's Telemetry service - provides support for metering, alarms, notifications, and other services.
* [**Keystone**](http://docs.openstack.org/developer/keystone/): OpenStack's Identity service - provides authentication, authorization and identity management
* [**CADF**](http://www.dmtf.org/standards/cadf): The Cloud Auditing Data Federation (CADF) standard defines a full event model anyone can use to fill in the essential data needed to certify, self-manage and self-audit application security in cloud environments.
* [**pyCADF**](https://pypi.python.org/pypi/pycadf): A python implementation of CADF events, maintained by OpenStack.

## Background, or Why is Keystone so special?

* _Why not just use pyCADF's audit middleware?_

Most OpenStack services (nova, glance, cinder, etc...) use `keystonemiddleware` to establish communication with Keystone. This is performed by creating a service user and saving credentials in the `[keystone_authtoken]` section and including `keystonemiddleware` in the service's pipeline. Services can also include `pyCADF`'s middleware, which piggybacks off the content it sees in `keystonemiddleware` to easily include auditing support for every API request that hits the service. We can't take this approach in Keystone. `keystonemiddleware` gets its information from a Keystone token (using the service user). If we try to include `pyCADF`'s middleware into Keystone's pipeline, then we'll run into a circular reference. What the Keystone team has decided to do is to instead emit notifications for specific events.

* _I thought Keystone already emits notifications, why do I need CADF?_

Keystone did already emit notifications, but these were lacking in content and made auditing impractical. Take for instance this example of a `basic` notification.

```json
{
    "event_type": "identity.user.created",
    "message_id": "0156ee79-b35f-4cef-ac37-d4a85f231c69",
    "payload": {
        "resource_info": "671da331c47d4e29bb6ea1d270154ec3"
    },
    "priority": "INFO",
    "publisher_id": "identity.host1234",
    "timestamp": "2013-08-29 19:03:45.960280"
}
```

We can tell a user was created, but who triggered the event? From which host machine? What project did they use as their scope? Using CADF notifications will tell us this information (and more), and additionally it'll standardize the way events are seen in OpenStack. On to the fun stuff!

## Viewing CADF notifications in Keystone logs

### Setup DevStack with Ceilometer added

Pull down devstack, add ceilometer, here's the `localrc` I used:

```ini
RECLONE=yes
ENABLED_SERVICES=key,ceilometer-acentral,ceilometer-acompute,ceilometer-alarm-evaluator,ceilometer-alarm-notifier,ceilometer-anotification,ceilometer-api,ceilometer-collector,g-api,g-reg,n-api,n-crt,n-obj,n-cpu,n-net,n-cond,cinder,c-sch,c-api,c-vol,n-sch,n-cauth,horizon,mysql,rabbit
SERVICE_TOKEN=openstack
ADMIN_PASSWORD=openstack
MYSQL_PASSWORD=openstack
RABBIT_PASSWORD=openstack
SERVICE_PASSWORD=openstack
LOGFILE=/opt/stack/logs/stack.sh.log
LIBS_FROM_GIT=python-keystoneclient,python-openstackclient
```

### Update Keystone's configuration file

Make the following changes to `keystone.conf`. Use `cadf` as the `notification_format` instead of `basic`. Also, set `notification_driver` twice, once to `messaging` and again to `log`. This will make the notifications be emitted to the `rabbitmq` service, and be written to Keystone's log.

```ini
notification_format = cadf
notification_driver = messaging
notification_driver = log
```

### Create a user

Now trigger an event that will generate a notification, creating a user should do the trick.

```bash
vagrant@vagrant-ubuntu-trusty-64:~/devstack$ . openrc admin admin
vagrant@vagrant-ubuntu-trusty-64:~/devstack$ openstack user create test_user --os-identity-api-version 3 --os-auth-url http://10.0.2.15:5000/v3 --os-default-domain default
```

### Examine the Keystone logs

If you look at the logs, you'll see something like this:

```
oslo.messaging.notification.identity.user.created [-] {"priority": "INFO", "event_type": "identity.user.created", "timestamp": "2015-07-05 00:36:27.337617", "publisher_id": "identity.vagrant-ubuntu-trusty-64.localdomain", "payload": {"typeURI": "http://schemas.dmtf.org/cloud/audit/1.0/event", "initiator": {"typeURI": "service/security/account/user", "host": {"agent": "python-keystoneclient", "address": "10.0.2.15"}, "project_id": "ae7e3ebab8f442a8a7f19bc25c8c09f0", "id": "ce14a6f356ef4c40ad93339e6e424a7f"}, "target": {"typeURI": "data/security/account/user", "id": "22a21766c47247028be3fda7ee0b712b"}, "observer": {"typeURI": "service/security", "id": "openstack:3bc912f3-5746-4e69-8e11-2510e9d78db2"}, "eventType": "activity", "eventTime": "2015-07-05T00:36:27.337390+0000", "action": "created.user", "outcome": "success", "id": "openstack:11fc2c26-0d67-4875-90d0-09186863e502", "resource_info": "22a21766c47247028be3fda7ee0b712b"}, "message_id": "9767db8f-1891-4566-b3a7-a217a37c515f"}
```

It looks like a CADF event to me! Here it is with better formatting:

```json
{
    "priority": "INFO",
    "event_type": "identity.user.created",
    "timestamp": "2015-07-05 00:36:27.337617",
    "publisher_id": "identity.vagrant-ubuntu-trusty-64.localdomain",
    "payload": {
        "typeURI": "http://schemas.dmtf.org/cloud/audit/1.0/event",
        "initiator": {
            "typeURI": "service/security/account/user",
            "host": {
                "agent": "python-keystoneclient",
                "address": "10.0.2.15"
            },
            "project_id": "ae7e3ebab8f442a8a7f19bc25c8c09f0",
            "id": "ce14a6f356ef4c40ad93339e6e424a7f"
        },
        "target": {
            "typeURI": "data/security/account/user",
            "id": "22a21766c47247028be3fda7ee0b712b"
        },
        "observer": {
            "typeURI": "service/security",
            "id": "openstack:3bc912f3-5746-4e69-8e11-2510e9d78db2"
        },
        "eventType": "activity",
        "eventTime": "2015-07-05T00:36:27.337390+0000",
        "action": "created.user",
        "outcome": "success",
        "id": "openstack:11fc2c26-0d67-4875-90d0-09186863e502",
        "resource_info": "22a21766c47247028be3fda7ee0b712b"
    },
    "message_id": "9767db8f-1891-4566-b3a7-a217a37c515f"
}
```

You'll see here that we have access to the necessary information we need for auditing. The `initiator` block includes: the user who performed the action, the project they used to authenticate with, and the host machine they used. For a full list of actions that will emit notifications, refer to [Keystone's notification documentation](http://docs.openstack.org/developer/keystone/event_notifications.html#id1).

## Viewing CADF notifications with Ceilometer

### Use Ceilometer's CLI

```bash
vagrant@vagrant-ubuntu-trusty-64:~/devstack$ ceilometer event-list --query event_type=identity.user.created
+-----------------------+---------------------------------------------------------------------------+
| Event Type            | Traits                                                                    |
+-----------------------+---------------------------------------------------------------------------+
| identity.user.created | +--------------+--------+-----------------------------------------------+ |
|                       | |     name     |  type  |                     value                     | |
|                       | +--------------+--------+-----------------------------------------------+ |
|                       | | initiator_id | string |        ce14a6f356ef4c40ad93339e6e424a7f       | |
|                       | |  project_id  | string |        ae7e3ebab8f442a8a7f19bc25c8c09f0       | |
|                       | | resource_id  | string |        22a21766c47247028be3fda7ee0b712b       | |
|                       | |   service    | string | identity.vagrant-ubuntu-trusty-64.localdomain | |
|                       | +--------------+--------+-----------------------------------------------+ |
+-----------------------+---------------------------------------------------------------------------+
```

## Viewing CADF notifications in RabbitMQ

Alternatively instead of using Ceilometer, you can use [RabbitMQ's](https://www.rabbitmq.com/) management plugin to view the same notifications. Note, you should **stop all Ceilometer processes** to ensure that it won't read the messages on the notification queue. _**Setup RabbitMQ management plugin**_ When installed, the RabbitMQ management plugin will automatically add a web interface, users will be able to easily read notifications.

```bash
vagrant@vagrant-ubuntu-trusty-64:~/devstack$ sudo rabbitmq-plugins enable rabbitmq_management
The following plugins have been enabled:
  mochiweb
  webmachine
  rabbitmq_web_dispatch
  amqp_client
  rabbitmq_management_agent
  rabbitmq_management
Plugin configuration has changed. Restart RabbitMQ for changes to take effect.
```

Restart RabbitMQ and ensure the management plugins are installed

```bash
vagrant@vagrant-ubuntu-trusty-64:~/devstack$ sudo service rabbitmq-server restart
 * Restarting message broker rabbitmq-server                                                                                                                                              [ OK ]

vagrant@vagrant-ubuntu-trusty-64:~/devstack$ rabbitmq-plugins list
[e] amqp_client                       3.2.4
[ ] cowboy                            0.5.0-rmq3.2.4-git4b93c2d
[ ] eldap                             3.2.4-gite309de4
[e] mochiweb                          2.7.0-rmq3.2.4-git680dba8
[ ] rabbitmq_amqp1_0                  3.2.4
[ ] rabbitmq_auth_backend_ldap        3.2.4
[ ] rabbitmq_auth_mechanism_ssl       3.2.4
[ ] rabbitmq_consistent_hash_exchange 3.2.4
[ ] rabbitmq_federation               3.2.4
[ ] rabbitmq_federation_management    3.2.4
[ ] rabbitmq_jsonrpc                  3.2.4
[ ] rabbitmq_jsonrpc_channel          3.2.4
[ ] rabbitmq_jsonrpc_channel_examples 3.2.4
[E] rabbitmq_management               3.2.4
[e] rabbitmq_management_agent         3.2.4
[ ] rabbitmq_management_visualiser    3.2.4
[ ] rabbitmq_mqtt                     3.2.4
[ ] rabbitmq_shovel                   3.2.4
[ ] rabbitmq_shovel_management        3.2.4
[ ] rabbitmq_stomp                    3.2.4
[ ] rabbitmq_tracing                  3.2.4
[e] rabbitmq_web_dispatch             3.2.4
[ ] rabbitmq_web_stomp                3.2.4
[ ] rabbitmq_web_stomp_examples       3.2.4
[ ] rfc4627_jsonrpc                   3.2.4-git5e67120
[ ] sockjs                            0.3.4-rmq3.2.4-git3132eb9
[e] webmachine                        1.10.3-rmq3.2.4-gite9359c7
```

### View CADF notifications in RabbitMQ's web console

You'll need to trigger another event, so create a user, project or role using OpenStackClient. When that is done, navigate to: `http://10.0.2.15:15672` and log in with your RabbitMQ credentials. The default username and password are `guest/guest`. Note, in the screenshots shown below RabbitMQ is shown running on port 8081, this because I had port forwarding enabled in my VM.

1. Check the `Queues` tab at the top

[![Screen Shot 2015-07-05 at 12.31.26 AM](http://developer.ibm.com/opentech/wp-content/uploads/sites/43/2015/07/Screen-Shot-2015-07-05-at-12.31.26-AM.png)](http://developer.ibm.com/opentech/wp-content/uploads/sites/43/2015/07/Screen-Shot-2015-07-05-at-12.31.26-AM.png)

1. Look for unread messages in the `notifications.info` queue.

[![Screen Shot 2015-07-05 at 12.30.43 AM](http://developer.ibm.com/opentech/wp-content/uploads/sites/43/2015/07/Screen-Shot-2015-07-05-at-12.30.43-AM.png)](http://developer.ibm.com/opentech/wp-content/uploads/sites/43/2015/07/Screen-Shot-2015-07-05-at-12.30.43-AM.png)

1. Get the messages, there will be authentication messages and a `identity.user.created` message

[![Screen Shot 2015-07-05 at 12.32.06 AM](http://developer.ibm.com/opentech/wp-content/uploads/sites/43/2015/07/Screen-Shot-2015-07-05-at-12.32.06-AM.png)](http://developer.ibm.com/opentech/wp-content/uploads/sites/43/2015/07/Screen-Shot-2015-07-05-at-12.32.06-AM.png)

1. View the payload of each message, which should be a CADF event

[![Screen Shot 2015-07-05 at 12.30.18 AM](http://developer.ibm.com/opentech/wp-content/uploads/sites/43/2015/07/Screen-Shot-2015-07-05-at-12.30.18-AM.png)](http://developer.ibm.com/opentech/wp-content/uploads/sites/43/2015/07/Screen-Shot-2015-07-05-at-12.30.18-AM.png)
