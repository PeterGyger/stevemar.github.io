---
title: "OpenStack development tips: Setting up a ZNC bouncer"
excerpt: "A guide to running your own ZNC bouncer."
tags:
  - software
  - openstack
---

_Originally posted on [https://developer.ibm.com/opentech/2016/01/21/openstack-development-tips-setting-up-a-znc-bouncer](https://developer.ibm.com/opentech/2016/01/21/openstack-development-tips-setting-up-a-znc-bouncer)_

Though not necessary, setting up a ZNC bouncer can positively impact your OpenStack development by making it easier for other developers to leave you messages while you’re away from IRC. You can also read important scroll back and logs easily to stay caught up in what's happening in your OpenStack project.

## Installing ZNC on your VM

This tutorial assumes you have access to a virtual machine with a public IP address. For my own purposes, I created a Ubuntu 14.04 VM on Bluemix using the m1.small flavor. Any VM on a bunch of public cloud providers would do just fine. I also opted to install from a repo instead of building from source, for no reason other than it is fewer steps. On the VM perform the following: 1) Add the repo instead of installing from source

```bash
ibmcloud@bouncer:~$ sudo add-apt-repository ppa:teward/znc
```

1. Update package lists

```bash
ibmcloud@bouncer:~$ sudo apt-get update
```

1. Install ZNC packages

```bash
ibmcloud@bouncer:~$ sudo apt-get install znc znc-dbg znc-dev znc-perl znc-python znc-tcl
```

## Configuring your ZNC server

1. Run `znc --makeconf` Don’t just run `znc`! Before running it for the first time, use the `--makeconf` argument. This takes the user through a first time configuration, most defaults are fine, the only required option is which port to run the service on. These are the options I specified:

```bash
port = 55901
username = stevemar
password = supersecretpassword
Setup a network? Yes (freenode is the first option)
```

Here's the entire output:

```bash
ibmcloud@bouncer:~$ znc --makeconf
[ .. ] Checking for list of available modules...
[ >> ] ok
[ ** ]
[ ** ] -- Global settings --
[ ** ]
[ ?? ] Listen on port (1025 to 65534): 55901
[ ?? ] Listen using SSL (yes/no) [no]:
[ ?? ] Listen using both IPv4 and IPv6 (yes/no) [yes]:
[ .. ] Verifying the listener...
[ >> ] ok
[ ** ] Unable to locate pem file: [/home/ibmcloud/.znc/znc.pem], creating it
[ .. ] Writing Pem file [/home/ibmcloud/.znc/znc.pem]...
[ >> ] ok
[ ** ] Enabled global modules [webadmin]
[ ** ]
[ ** ] -- Admin user settings --
[ ** ]
[ ?? ] Username (alphanumeric): stevemar
[ ?? ] Enter password:
[ ?? ] Confirm password:
[ ?? ] Nick [stevemar]:
[ ?? ] Alternate nick [stevemar_]:
[ ?? ] Ident [stevemar]:
[ ?? ] Real name [Got ZNC?]:
[ ?? ] Bind host (optional):
[ ** ] Enabled user modules [chansaver, controlpanel]
[ ** ]
[ ?? ] Set up a network? (yes/no) [yes]:
[ ** ]
[ ** ] -- Network settings --
[ ** ]
[ ?? ] Name [freenode]:
[ ?? ] Server host [chat.freenode.net]:
[ ?? ] Server uses SSL? (yes/no) [yes]:
[ ?? ] Server port (1 to 65535) [6697]:
[ ?? ] Server password (probably empty):
[ ?? ] Initial channels:
[ ** ] Enabled network modules [simple_away]
[ ** ]
[ .. ] Writing config [/home/ibmcloud/.znc/configs/znc.conf]...
[ >> ] ok
[ ** ]
[ ** ] To connect to this ZNC you need to connect to it as your IRC server
[ ** ] using the port that you supplied.  You have to supply your login info
[ ** ] as the IRC server password like this: user/network:pass.
[ ** ]
[ ** ] Try something like this in your IRC client...
[ ** ] /server <znc_server_ip> 55901 stevemar: <pass>[ ** ]
[ ** ] To manage settings, users and networks, point your web browser to
[ ** ] http://<znc_server_ip>:55901/
[ ** ]
[ ?? ] Launch ZNC now? (yes/no) [yes]:
[ .. ] Opening config [/home/ibmcloud/.znc/configs/znc.conf]...
[ >> ] ok
[ .. ] Loading global module [webadmin]...
[ >> ] [/usr/lib/znc/webadmin.so]
[ .. ] Binding to port [55901]...
[ >> ] ok
[ ** ] Loading user [stevemar]
[ ** ] Loading network [freenode]
[ .. ] Loading network module [simple_away]...
[ >> ] [/usr/lib/znc/simple_away.so]
[ .. ] Adding server [chat.freenode.net +6697 ]...
[ >> ] ok
[ .. ] Loading user module [chansaver]...
[ >> ] ok
[ .. ] Loading user module [controlpanel]...
[ >> ] ok
[ .. ] Forking into the background...
[ >> ] [pid: 13439]
[ ** ] ZNC 1.6.1+deb1~ubuntu14.04.0 - http://znc.in</znc_server_ip></pass></znc_server_ip>
```

1. Allow traffic through firewall By default, VMs on Bluemix have the firewall enabled, we’ll need to allow traffic through port 55901, since that’s the port we specified.

```bash
ibmcloud@bouncer:~$ sudo ufw allow 55901
```

## Using ZNC's Web Interface

One of the best features about ZNC is the web admin interface, just go to: `http://ip_address:55901` from any machine and log in with the username and password that you created in the configuration step. From here you can modify a bunch of settings. The only one I chose to modify was the buffer, I increased it to 500.

[![znc webui](https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/01/znc-webui-1024x972.png)](https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/01/znc-webui.png)

## Connecting to your ZNC instance

On my laptop I use LimeChat as my IRC client. Connecting to my ZNC server is pretty easy, I just create a new IRC server instance and specify the IP address, username, and password. If your nickname is registered with the NickServ, then you may also need to identify upon logging in.

> Limechat settings

[![limechat-server](https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/01/limechat-server-783x1024.png)](https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/01/limechat-server.png)

> More Limechat settings

[![limechat-config](https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/01/limechat-config-795x1024.png)](https://developer.ibm.com/opentech/wp-content/uploads/sites/43/2016/01/limechat-config.png)

## References

I used the following sites when installing and setting up ZNC: [https://dague.net/2014/09/13/my-irc-proxy-setup/](https://dague.net/2014/09/13/my-irc-proxy-setup/) and [http://wiki.znc.in/Installation#Install_via_PPA](http://wiki.znc.in/Installation#Install_via_PPA)
