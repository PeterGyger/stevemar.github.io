---
title: "Adventures using IBM's Kubernetes service"
excerpt: "Lessons learned from my trials and tribulations using IBM's Kubenetes service"
tags: 
  - software
  - ibm
  - ibm-cloud
image:
  path: /images/pyk8s.png
  thumbnail: /images/pyk8s.png
---

## My flasky app

Custom linter, think like JSON-lint but for frontmatter, a mix of Markdown and YAML. We ended up using flask, JSONSchema, and markdownlint (yes, the node based one) all over the place. 

## Building image and testing locally

```
docker build -t validator:latest .
docker run -d -p 5000:5000 validator
```

## Push to IBM Cloud Kubernetes Service

### Prereqs

```bash
ibmcloud plugin install container-registry -r Bluemix
ibmcloud plugin install container-service -r Bluemix
```

### Push to IBM Cloud Container Registry 

```bash
ibmcloud cr namespace-add aida
ibmcloud cr login
docker tag validator registry.ng.bluemix.net/aida/validator
docker push registry.ng.bluemix.net/aida/validator
```

### Deploy to IKS

Create the cluster in the UI -- named "validator-beta"

### Download and use the cluster config

```bash
ibmcloud ks cluster-config validator-beta
export KUBECONFIG=/Users/stevemar/.bluemix/plugins/container-service/clusters/validator-beta/kube-config-dal10-validator-beta.yml
```

### Deploy by pointing to the Container Registry

```bash
kubectl run validator-deployment --image=registry.ng.bluemix.net/aida/validator
kubectl expose deployment/validator-deployment --type=NodePort --port=5000 --name=validator-service --target-port=5000
```

### Find the External IP and port

```bash
ibmcloud ks workers --cluster validator-beta
ID                                                 Public IP        Private IP       Machine Type        State    Status   Zone    Version   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w1   169.xx.yy.116    10.177.184.133   u2c.2x4.encrypted   normal   Ready    dal10   1.10.12_1543   
kube-dal10-cre853b87cc0d44926975ee5a41044b1e8-w2   169.xx.yy.241   10.177.184.141   u2c.2x4.encrypted   normal   Ready    dal10   1.10.12_1543
```

```
kubectl describe service validator-service
Name:                     validator-real-service
Namespace:                default
Labels:                   run=validator-service
Annotations:              <none>
Selector:                 run=validator-service
Type:                     NodePort
IP:                       172.21.189.136
Port:                     <unset>  5000/TCP
TargetPort:               5000/TCP
NodePort:                 <unset>  31760/TCP
Endpoints:                172.30.238.200:5000
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

Hit http://169.xx.yy.116:31760/ and see whats up

## Push a new version

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

I'm hoping to cram this into a shell script that Travis can run, this is instead of using the Delivery Pipeline. And figure out how to host this on a proper URL instead of an IP address.

Overall, pretty positive, though.

