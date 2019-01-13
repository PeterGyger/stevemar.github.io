---
title: "Connect to IBM's LDAP, Bluepages, with Python"
excerpt: "The least amount of Python you need to write to connect to IBM's LDAP -- Bluepages"
tags: 
  - software
  - ibm
  - python
  - ldap
image:
  path: /images/bluepages-banner.png
  thumbnail: /images/bluepages-banner.png
---

If you've ever worked at IBM then you know what Bluepages is, for those who don't, it's the company's LDAP server. It's most commonly used via a web interface where employees can look up other employees. Check it out below.

![bluepages]({{'/images/bluepages.png'}})

As part of my day job I help run the [IBM Developer](https://developer.ibm.com/) site, which lists a bunch of our [Developer Advocates](https://developer.ibm.com/profiles/). Recently someone left the company and their page remained until someone made a PR to remove it. I saw this as an opportunity to ideate how I would improve things. A bit of Python and a travis job that runs daily to remove users that are not found should do the trick!

So I quickly wrote some python code using the [python-ldap](https://github.com/python-ldap/python-ldap) project, which wraps the [openLDAP](https://www.openldap.org/) client, so ensure you have those two installed before looking at the Python code below.

```bash
# I use a Mac, so brew it is
brew install openldap
pip install python-ldap
```

Then fire up your favorite editor and write a few lines of code to anonymously bind and look up a user. You can look up a user by email using the `email=*` query or by name using `cn=*`.

```python
import ldap

ldap_uri = 'ldap://bluepages.ibm.com'
ldap_base = 'ou=bluepages,o=ibm.com'
#query = "(cn=Steve Martinelli)"
query = "(email=stevemar@caibm.com)"

con = ldap.initialize(ldap_uri)
result = con.search_s(ldap_base, ldap.SCOPE_SUBTREE, query)
print(result)
```

You'll get back something like this:

```json
{
  "ou": ["bluepages"],
  "o": ["ibm.com"],
  "co": ["Canada"],
  "emailAddress": ["stevemar@ca.ibm.com"],
  "cn": ["Steve Martinelli"]
}
```

What I like about the python-ldap library is that it makes things simple. Even after working for years on [OpenStack's Identity service](https://github.com/openstack/keystone/blob/master/keystone/identity/backends/ldap/core.py) I still scratch my head if given too many prompts. 

Hope this helps other IBMers looking to whip up a quick prototype or two!
