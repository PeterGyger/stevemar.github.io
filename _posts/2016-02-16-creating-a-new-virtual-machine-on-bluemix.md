---
title: "Creating a new virtual machine on Bluemix"
excerpt: "A quick run down on how to spin up a new virtual machine on Bluemix, IBM's Cloud Platform, in a few minutes."
tags: 
  - software
  - ibm
  - openstack
  - ibm cloud
image:
  path: https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/02/bm_splash.png
  thumbnail: https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/02/bm_splash.png
---

_Originally posted on [https://developer.ibm.com/opentech/2016/02/16/creating-a-new-virtual-machine-on-bluemix](https://developer.ibm.com/opentech/2016/02/16/creating-a-new-virtual-machine-on-bluemix)_

First impressions are pretty important. Bluemix, IBMs one-stop-shop for all things cloud has been out for a while now, I’ve played with it when it was in closed-beta, but now it’s out in the wild! I figured I would write about my thoughts using the service, specifically I’ll take a look at the simple task of requesting a virtual machine (VM). My main use case for wanting to create a VM on bluemix is to [setup a ZNC bouncer](https://developer.ibm.com/opentech/2016/01/21/openstack-development-tips-setting-up-a-znc-bouncer/), I needed a VM on a cloud that'll give me a public IP address so I can check OpenStack development happening on IRC from anywhere -- my laptop or even my phone.

# Accessing Bluemix

It's worth noting that Bluemix is built on Cloud Foundry and powered by OpenStack, which is all kinds of awesome! To get started, I went to [bluemix.net](http://bluemix.net), I was able to log in using my IBM ID (which isn’t my work credential, anyone can have an IBM ID, but if you’re an employee it is linked to your employee ID -- magic).

[![bm_splash](https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/02/bm_splash-1024x546.png)](https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/02/bm_splash.png)

Upon logging in, we are presented with a dashboard, it contains a bunch of shortcuts to common actions and an overview of things you’ve created. I really like the shortcuts at the top, _Create App, Start Containers, Run Virtual Machines_, etc, it’s all there, no digging around the menus trying to figure out where to go. We can also see the VM I created earlier, my trusty ZNC bouncer. So let’s try to create another VM.

[![bm_dash](https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/02/bm_dash-1024x401.png)](https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/02/bm_dash.png)

# Create your keypair

Before creating the VM, we have to create a public/private keypair so we can securely connect to our new host. On your personal computer, create a public/private key pair, I omitted some of the output for the sake of readability. But essentially, my public key that I’ll upload to Bluemix is at `/Users/stevemar/.ssh/test_bm.pub`

```bash
smartinelli-mac:~ stevemar$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/stevemar/.ssh/id_rsa): /Users/stevemar/.ssh/test_bm
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /Users/stevemar/.ssh/test_bm.
Your public key has been saved in /Users/stevemar/.ssh/test_bm.pub
```

# Customize your VM

Now let’s actually create the VM in Bluemix. Upon clicking the _Run Virtual Machines_ button, I’m presented with a panel that allows me customize my new VM, which I’ve aptly named _test_bm_. I’m pleased to see that there are a handful of Linux images available by default. I went with Ubuntu 14.04\. It looks like you can also upload and create a new image, I did not try this option. By picking the Ubuntu 14.04 image, the UI informs me that a default non-root user already created, this user is named _ibmcloud_. There were also only two flavors available, _m1.small_ and _m1.medium_. Since this was only a test, I selected the _m1.small_ option.

[![bm_wiz](https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/02/bm_wiz-1024x509.png)](https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/02/bm_wiz.png)

# Import your key

Before selecting the _Create_ option, I had to add a new public keypair. To do this, select the _Add Key_ option, which brings up the following panel:

[![bm_key](https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/02/bm_key-1022x1024.png)](https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/02/bm_key.png)

I gave my keypair a simple name, and copied the contents of `/Users/stevemar/.ssh/test_bm.pub` into the text box. An example of what a public key looks like can be seen below, again trimmed for the sake of readability:

```bash
smartinelli-mac:~ stevemar$ cat /Users/stevemar/.ssh/test_bm.pub 
ssh-rsa AAAAB3NzaC1...4ZKi3BnxXU/ stevemar@smartinelli-mac
```

Now that you’ve imported your public key, go ahead and press that _CREATE_ button!

# Viewing your VM

Once the VM has been created, you’ll be directed to a page that shows VM information. Not much to do on this page; there are options to delete, stop, restart and suspend your VM, but let’s try accessing it. The public IP is available at the top of the page.

[![bm_summary](https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/02/bm_summary-1024x231.png)](https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/02/bm_summary.png)

# Accessing your VM

From a terminal use `ssh`. In my case, I’m using a non-default location for my keypair, and remember, we have to specify the _ibmcloud_ account when accessing the VM.

```bash
smartinelli-mac:~ stevemar$ ssh ubuntu@public_ip -i ~/.ssh/test_bm
The authenticity of host 'public_ip (public_ip)' can't be established.
RSA key fingerprint is 22:06:7f:d6:98:ad:7f:20:82:0e:4d:13:d0:c4:34:79.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'public_ip' (RSA) to the list of known hosts.
Welcome to Ubuntu 14.04.1 LTS (GNU/Linux 3.13.0-24-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

ubuntu@test-bm:~$ 
```

Voilà! Now you have your very own VM on a public cloud, maybe it's time to [setup that ZNC bouncer](https://developer.ibm.com/opentech/2016/01/21/openstack-development-tips-setting-up-a-znc-bouncer/)?

# A few final thoughts

Given the VM service in Bluemix is in open beta, a few hiccups are expected. Two flavors seems okay for now, but there definitely need to be more, a nano sized flavor would be sufficient for a ZNC bouncer. Also, there seems to be no way to manage keys once created, I wasn’t able to delete the keys I had imported. On a positive note, I am pleased to say that I’ve been running my bouncer for a while, and downtime hasn’t been an issue at all.

# References

Matt Kelm also has a write up on how to [import different images](https://developer.ibm.com/bluemix/2016/01/28/deploy-openstack-app-in-minutes/) and use them instead.
