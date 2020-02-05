---
title: "Debugging keystone tests and live deployments"
excerpt: "Unit tests, functional tests, on a web server or eventlet, we've got you covered"
tags:
  - software
  - openstack
---

_Originally posted on [https://developer.ibm.com/opentech/2015/04/27/debugging-keystone-tests-and-live-deployments/](https://developer.ibm.com/opentech/2015/04/27/debugging-keystone-tests-and-live-deployments/)_

Debugging an application is sometimes necessary, and keystone (OpenStack's Identity service) is like any other, though it does have it's quirks. For the most part, developers will just add in `import pdb; pdb.set_trace()` and run the application, resulting in an interactive prompt.

## Debugging keystone tests

### The wrong way

Say a developer wants to debug a keystone federation test, `test_create_idp` in `test_v3_federation.py`. Most will use python's debugger, pdb.

```python
def test_create_idp(self):
"""Creates the IdentityProvider entity associated to remote_ids."""

import pdb; pdb.set_trace()
keys_to_check = list(self.idp_keys)
body = self.default_body.copy()
body['description'] = uuid.uuid4().hex
```

However, if a developer runs `tox -e py27 test_v3_federation` to run the unit tests, with the above changes, they'll see the following output:

```bash
steve$ tox -e py27 test_v3_federation
...
Captured traceback:
~~~~~~~~~~~~~~~~~~~
    Traceback (most recent call last):
      File "keystone/tests/unit/test_v3_federation.py", line 2001, in setUp
        super(FederatedTokenTests, self).setUp()
      File "keystone/tests/unit/test_v3.py", line 158, in setUp
        super(RestfulTestCase, self).setUp(app_conf=app_conf)
      File "keystone/tests/unit/rest.py", line 66, in setUp
        self.load_fixtures(default_fixtures)
      File "keystone/tests/unit/test_v3_federation.py", line 2032, in load_fixtures
        self.load_federation_sample_data()
      File "keystone/tests/unit/test_v3_federation.py", line 669, in load_federation_sample_data
        self._inject_assertion(context, variant)
      File "keystone/tests/unit/test_v3_federation.py", line 184, in _inject_assertion
        import pdb; pdb.set_trace()
      File "/usr/lib/python2.7/bdb.py", line 53, in trace_dispatch
        return self.dispatch_return(frame, arg)
      File "/usr/lib/python2.7/bdb.py", line 91, in dispatch_return
        if self.quitting: raise BdbQuit
    bdb.BdbQuit
```

This is caused by an issue with testr and testtools, it is further discussed [on the OpenStack Wiki](https://wiki.openstack.org/wiki/Testr#Debugging_.28pdb.29_Tests)

### The right way

What a developer should do is use Oslo Test's debug helper, `oslo_debug_helper`. Simply run tox with the debug option instead of py27. Voilà! Your own debugging prompt.

```bash
steve$ tox -e debug test_v3_federation
debug develop-inst-nodeps: /opt/stack/keystone
debug runtests: commands[0] | oslo_debug_helper test_v3_federation
Tests running...
--Return--
/opt/stack/keystone/keystone/tests/unit/test_v3_federation.py(184)_inject_assertion();None
import pdb; pdb.set_trace()
(Pdb)
```

There is additional documentation on how to add the debug environment to any OpenStack project by viewing the [Oslo Test documentation](http://docs.openstack.org/developer/oslotest/features.html#debugging-with-oslo-debug-helper).

## Debugging live keystone deployments

Now let's say a deployer want to debug a live server where keystone is running under Apache (the recommended way to deploy keystone).

### The wrong way

Like the above example, most folks will use pdb; let's try using pdb and to try debugging an issue with listing users.

```python
@controller.filterprotected('domain_id', 'enabled', 'name')
def list_users(self, context, filters):
    import pdb; pdb.set_trace()
```

Restart Apache so the changes take effect.

```bash
steve:devstack$ sudo service apache2 restart
 * Restarting web server apache2 - [ OK ]
```

Attempting to list users will result in an error and upon checking the logs, a similar exception to the one seen above will be logged.

```bash
steve$ openstack user list
WARNING: openstackclient.shell The volume version  is not in supported versions
ERROR: openstack An unexpected error prevented the server from fulfilling your request:  (Disable debug mode to suppress these details.) (HTTP 500) (Request-ID: req-7795e720-0196-4201-8a0a-44a136f4449e)
```

### The right way

Use [rpdb](https://pypi.python.org/pypi/rpdb/) instead of pdb to remotely debug an application running under Apache. (Note, to install rpdb, simply run: `pip install rpdb`.) Simply drop in `import rpdb; rpdb.set_trace()` instead of the usual `import pdb; pdb.set_trace()`. Attempting to run `$ openstack user list` again will cause the terminal to hang, this is expected. The keystone log will show a message similar to: `2015-04-27 02:02:44.041686 pdb is running on 127.0.0.1:4444`. Attempt to connect to this service using the `nc` command in another terminal.

```bash
steve$ nc 127.0.0.1 4444
/opt/stack/keystone/keystone/identity/controllers.py(221)list_users()
hints = UserV3.build_driver_hints(context, filters)
(Pdb)
```

Now you may debug your application. Happy debugging!
