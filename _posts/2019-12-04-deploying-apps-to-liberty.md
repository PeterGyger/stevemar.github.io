---
title: "Deploying an app to WebSphere Liberty"
excerpt: "Step by step guide to getting an app to WebSphere Liberty"
tags: 
  - software
  - ibm
image:
  path: /images/websphere-liberty-logo.png
  thumbnail: /images/websphere-liberty-logo.png
---

These are my rough notes on how I got Liberty up and running on my Ubuntu 18.04 box. These are the steps I did:

### Steps

Get the WebSphere Liberty Profile installer, this location may change in future :shrug:

```bash
wget https://public.dhe.ibm.com/ibmdl/export/pub/software/websphere/wasdev/downloads/wlp/19.0.0.11/wlp-webProfile8-java8-linux-x86_64-19.0.0.11.zip
```

There is no "install" persay, just unzip it and run it.

> NOTE: If using minimal Ubuntu, run `apt update` and `apt install unzip` to get unzip on your system.

```bash
unzip wlp-webProfile8-java8-linux-x86_64-19.0.0.11.zip
cd wlp
```

Start the server

```bash
./bin/server start
```

By default, the server can only be accessed by the localhost, make it available by editing `server.xml` and adding `hosts="*"` in the endpoint section.

```bash
vi usr/servers/defaultServer/server.xml
```

Test that the default server page shows up by opening a browser and going to `http://ip-address:9080`, or by `curl`.

```bash
curl http://ip-address:9080
```

To add an app, get a WAR, EAR, or JAR file, in this case we'll use one called *Mod Resorts*

```bash
wget https://github.com/IBM/appmod-resorts/raw/master/data/examples/modresorts-1.0.war
```

Now, the best part, just drop it into the `dropins` directory.

```bash
mv ~/modresorts-1.0.war usr/servers/defaultServer/dropins
```

Test that the default server page shows up by opening a browser and going to `http://ip-address:9080/resorts`, or by `curl`.

```bash
curl http://ip-address:9080/resorts
```
