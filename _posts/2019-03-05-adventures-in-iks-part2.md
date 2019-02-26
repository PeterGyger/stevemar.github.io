---
title: "Adventures using IBM's Kubernetes service - Part 2"
excerpt: "Lessons learned from my trials and tribulations using IBM's Kubenetes service"
tags: 
  - software
  - ibm
  - ibm-cloud
image:
  path: /images/pyk8s.png
  thumbnail: /images/pyk8s.png
---

## Background

This is a continuation of a [previous post](http://www.stevemar.net/adventures-in-iks-part1/)! In the previous blog post I wrote about deploying a Flask app using Docker and IBM Cloud's Kubernetes service. In this post I'll write about updating that deployment.

The gist of what I want to accomplish is:

* Rebuild the Docker image
* Tag a new image version
* Push it to the IBM Container Registry
* Update the Kubernetes workers

## Pushing a new version

### Rebuild the docker image and push to container registry

Much like in the previous blog post, rebuilding, tagging, and pushing the image can be done in a few commands:

```bash
docker build -t validator:latest .
ibmcloud cr login
docker tag validator registry.ng.bluemix.net/aida/validator
docker push registry.ng.bluemix.net/aida/validator
```

### Find workers

Now let's update our workers, first we have to find the workers associated with the cluster, run the `ibmcloud ks workers` command to see the two IDs for my workers: `kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w1` and `kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w2`.

```bash
ibmcloud ks workers --cluster validator-beta
ID                                                 Public IP        Private IP       Machine Type        State    Status   Zone    Version   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w1   169.xx.yy.116    10.177.184.133   u2c.2x4.encrypted   normal   Ready    dal10   1.10.12_1543*   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w2   169.xx.yy.241   10.177.184.141   u2c.2x4.encrypted   normal   Ready    dal10   1.10.12_1543*
```

### Update the workers

To update the workers, run the `ibmcloud ks worker-update` command while passing in your cluster name and the workers you want to update.

> :bulb: the `-f` option is to bypass the user prompt

```
ibmcloud ks worker-update -f --cluster validator-beta --workers kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w1,kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w2
```

### Viewing the workers while updating.

I figured I would add this piece as I found it interesting. You can view the `State` columns change as the worker update is being performed. `State` is changed from `normal` to `reload_pending` to `reloading` and finally back to `normal`, one worker at a time. 

#### Before the update

```
ibmcloud ks workers --cluster validator-beta
ID                                                 Public IP        Private IP       Machine Type        State    Status   Zone    Version   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w1   169.xx.yy.116    10.177.184.133   u2c.2x4.encrypted   normal   Ready    dal10   1.10.12_1543*   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w2   169.xx.yy.241   10.177.184.141   u2c.2x4.encrypted   normal   Ready    dal10   1.10.12_1543*   
```

#### During the update

```bash
$ ibmcloud ks workers --cluster validator-beta
OK
ID                                                 Public IP        Private IP       Machine Type        State            Status   Zone    Version   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w1   169.xx.yy.116    10.177.184.133   u2c.2x4.encrypted   reload_pending   -        dal10   1.10.12_1543 --> 1.10.12_1544 (pending)   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w2   169.xx.yy.241   10.177.184.141   u2c.2x4.encrypted   normal           Ready    dal10   1.10.12_1543 --> 1.10.12_1544 (pending)   

$ ibmcloud ks workers --cluster validator-beta
OK
ID                                                 Public IP        Private IP       Machine Type        State       Status   Zone    Version   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w1   169.xx.yy.116    10.177.184.133   u2c.2x4.encrypted   reloading   -        dal10   1.10.12_1543 --> 1.10.12_1544 (pending)   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w2   169.xx.yy.241   10.177.184.141   u2c.2x4.encrypted   normal      Ready    dal10   1.10.12_1543 --> 1.10.12_1544 (pending)   

$ ibmcloud ks workers --cluster validator-beta
OK
ID                                                 Public IP        Private IP       Machine Type        State       Status   Zone    Version   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w1   169.xx.yy.116    10.177.184.133   u2c.2x4.encrypted   normal      Ready    dal10   1.10.12_1544   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w2   169.xx.yy.241   10.177.184.141   u2c.2x4.encrypted   reloading   -        dal10   1.10.12_1543 --> 1.10.12_1544 (pending)
```

#### After the update

```
$ ibmcloud ks workers --cluster validator-beta
OK
ID                                                 Public IP        Private IP       Machine Type        State    Status   Zone    Version   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w1   169.xx.yy.116    10.177.184.133   u2c.2x4.encrypted   normal   Ready    dal10   1.10.12_1544   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w2   169.xx.yy.241   10.177.184.141   u2c.2x4.encrypted   normal   Ready    dal10   1.10.12_1544   
```

### Make it copy-pasta friendly

I've now gotten into the habbit of dumping the following into a terminal whenever I want a new version of the app online:

```bash
git pull origin master
docker build -t validator:latest .
docker tag validator registry.ng.bluemix.net/aida/validator
docker push registry.ng.bluemix.net/aida/validator
ibmcloud ks worker-update -f --cluster validator-beta --workers kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w1,kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w2
```

### Thoughts

This is definitely not production worthy but fine for an internal tool. I'm hoping to write up about the following as I solve these problems in the near future:

* Pushing a new image on every commit (presently can't do this because the code is in Enterprise Github).
* Figure out how to host this on a proper URL instead of an IP address.
* Read up on how to tag image versions.

Overall, pretty positive experience with Kubernetes and IBM Cloud's Kubernetes Service.
