---
title: "Updating IBM Cloud CLI plugins"
excerpt: "CLI Adventures: The IBM Cloud CLI edition"
tags: 
  - software
  - ibm
  - ibm-cloud
image:
  path: /images/ibm-cloud-cli-plugins.png
  thumbnail: /images/ibm-cloud-cli-plugins.png
---

So I had to update the IBM Cloud CLI. Easy right? Well, sorta. Now I know I had a few extra plugins (cloud-functions, container-service, and container-registry) in addition to the CLI, I was hoping those would be updated at the same time.

```
$ ibmcloud update
Checking for updates...
No update required. Your CLI is already up-to-date.
```

Wait, what? Maybe I should just re-install the CLI, that should trigger the plugins to update. Let's see what happened. I am splitting the output in four parts.

#### Main CLI, appears up to date

```bash
$ curl -sL https://ibm.biz/idt-installer | bash
[main] --==[ IBM Cloud Developer Tools for Linux/MacOS - Installer, v1.2.3 ]==--
[install] Starting Installation...
[install_bx] Updating existing IBM Cloud 'bx' CLI...
Checking for updates...
No update required. Your CLI is already up-to-date.
```

#### The cloud-functions plugin, which didn't update

```bash
[install_plugins] Installing plugin 'cloud-functions'
Looking up 'cloud-functions' from repository 'IBM Cloud'...
Plug-in 'cloud-functions 1.0.28' found in repository 'IBM Cloud'
Plug-in 'cloud-functions/wsk/functions/fn 1.0.22' was already installed. Do you want to update it with 'cloud-functions 1.0.28' or not? [y/N]> 
FAILED
Could not read from input: EOF
```

#### The container-registry plugin, which did update

```bash
[install_plugins] Checking status of plugin: container-registry
[install_plugins] Updating plugin 'container-registry' from version '0.1.358   Update Available'
Plug-in 'container-registry 0.1.358' was installed.
Checking upgrades for plug-in 'container-registry' from repository 'IBM Cloud'...
Update 'container-registry 0.1.358' to 'container-registry 0.1.360'
Attempting to download the binary file...
 26.96 MiB / 26.96 MiB [=============================================================================================] 100.00% 8s
28264776 bytes downloaded
Updating binary...
OK
The plug-in was successfully upgraded.
```

#### The container-service plugin, which didn't update

```bash
[install_plugins] Checking status of plugin: container-service
[install_plugins] Installing plugin 'container-service'
Looking up 'container-service' from repository 'IBM Cloud'...
Plug-in 'container-service/kubernetes-service 0.2.30' found in repository 'IBM Cloud'
Plug-in 'container-service/kubernetes-service 0.1.593' was already installed. Do you want to update it with 'container-service/kubernetes-service 0.2.30' or not? [y/N]> 
FAILED
Could not read from input: EOF
```

#### And a summary message

```bash
Plugin Name                            Version   Status   
dev                                    2.1.12       
sdk-gen                                0.1.12       
cloud-functions/wsk/functions/fn       1.0.22    Update Available   
container-registry                     0.1.360      
container-service/kubernetes-service   0.1.593   Update Available   

[install_plugins] Finished installing/updating plugins
[install] Install finished.
[main] --==[ Total time: 24 seconds ]==--
```

Why one plugin updated and two didn't? No idea!


#### Update the individual plugins by running:

```bash
ibmcloud plugin install X
```

#### container-registry

```bash
$ ibmcloud plugin install container-registry
Looking up 'container-registry' from repository 'IBM Cloud'...
Plug-in 'container-registry 0.1.360' found in repository 'IBM Cloud'
Plug-in 'container-registry 0.1.360' was already installed. Do you want to re-install it or not? [y/N]> y
Attempting to download the binary file...
 26.96 MiB / 26.96 MiB [=============================================================================================================================================] 100.00% 6s
28264776 bytes downloaded
Installing binary...
OK
Plug-in 'container-registry 0.1.360' was successfully installed into /Users/stevemar/.bluemix/plugins/container-registry. Use 'ibmcloud plugin show container-registry' to show its details.
```

#### container-service

```
$ ibmcloud plugin install container-service
Looking up 'container-service' from repository 'IBM Cloud'...
Plug-in 'container-service/kubernetes-service 0.2.30' found in repository 'IBM Cloud'
Plug-in 'container-service/kubernetes-service 0.1.593' was already installed. Do you want to update it with 'container-service/kubernetes-service 0.2.30' or not? [y/N]> y
Attempting to download the binary file...
 22.85 MiB / 22.85 MiB [=============================================================================================================================================] 100.00% 5s
23960360 bytes downloaded
Installing binary...
OK
Plug-in 'container-service 0.2.30' was successfully installed into /Users/stevemar/.bluemix/plugins/container-service. Use 'ibmcloud plugin show container-service' to show its details.
```

#### cloud-functions

```bash
$ ibmcloud plugin install cloud-functions
Looking up 'cloud-functions' from repository 'IBM Cloud'...
Plug-in 'cloud-functions 1.0.28' found in repository 'IBM Cloud'
Plug-in 'cloud-functions/wsk/functions/fn 1.0.22' was already installed. Do you want to update it with 'cloud-functions 1.0.28' or not? [y/N]> y
Attempting to download the binary file...
 11.57 MiB / 11.57 MiB [=============================================================================================================================================] 100.00% 3s
12127792 bytes downloaded
Installing binary...
OK
Plug-in 'cloud-functions 1.0.28' was successfully installed into /Users/stevemar/.bluemix/plugins/cloud-functions. Use 'ibmcloud plugin show cloud-functions' to show its details.
```

#### Final list of plugins

```bash
$ ibmcloud plugin list
Listing installed plug-ins...

Plugin Name                            Version   Status   
dev                                    2.1.12       
sdk-gen                                0.1.12       
cloud-functions/wsk/functions/fn       1.0.28       
container-registry                     0.1.360      
container-service/kubernetes-service   0.2.30       
```
