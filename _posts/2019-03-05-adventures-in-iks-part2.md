---
title: "Adventures using IBM's Kubernetes service - Part 1"
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

This is a continuation of a previous post!

## Pushing a new version

### Rebuild the docker image and push to container registry

```bash
docker build -t validator:latest .
ibmcloud cr login
docker tag validator registry.ng.bluemix.net/aida/validator
docker push registry.ng.bluemix.net/aida/validator
```

### Find workers

```bash
ibmcloud ks workers --cluster validator-beta
ID                                                 Public IP        Private IP       Machine Type        State    Status   Zone    Version   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w1   169.xx.yy.116    10.177.184.133   u2c.2x4.encrypted   normal   Ready    dal10   1.10.12_1543*   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w2   169.xx.yy.241   10.177.184.141   u2c.2x4.encrypted   normal   Ready    dal10   1.10.12_1543*
```

### Update the workers

Before

```
ibmcloud ks workers --cluster validator-beta
ID                                                 Public IP        Private IP       Machine Type        State    Status   Zone    Version   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w1   169.xx.yy.116    10.177.184.133   u2c.2x4.encrypted   normal   Ready    dal10   1.10.12_1543*   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w2   169.xx.yy.241   10.177.184.141   u2c.2x4.encrypted   normal   Ready    dal10   1.10.12_1543*   
```

During

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

After
```
$ ibmcloud ks workers --cluster validator-beta
OK
ID                                                 Public IP        Private IP       Machine Type        State    Status   Zone    Version   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w1   169.xx.yy.116    10.177.184.133   u2c.2x4.encrypted   normal   Ready    dal10   1.10.12_1544   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w2   169.xx.yy.241   10.177.184.141   u2c.2x4.encrypted   normal   Ready    dal10   1.10.12_1544   
```

### As a copy-pasta friendly bit

```bash
git pull origin master
docker build -t validator:latest .
docker tag validator registry.ng.bluemix.net/aida/validator
docker push registry.ng.bluemix.net/aida/validator
ibmcloud ks worker-update -f --cluster validator-beta --workers kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w1,kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w2
```

### Thoughts

I'm hoping to cram this into a shell script that Travis can run, this is instead of using the Delivery Pipeline. And figure out how to host this on a proper URL instead of an IP address. Bad tagging.

Overall, pretty positive, though.
