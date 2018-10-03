---
title: "Create a cloud native MERN application from a starter kit"
excerpt: "An announcement blog for my latest Code Pattern, a MERN application"
tags: 
  - software
  - ibm
  - ibm cloud
  - mern
image:
  path: /images/web-starter-kits.png
  thumbnail: /images/web-starter-kits.png
---

_by Steve Martinelli and Emma Tucker_

With Node.js and MongoDB becoming ever more popular techonlogies the MERN stack is an essential tool for the modern developer. In the [MERN Code Pattern](https://github.com/IBM-Cloud/MERN-app) we cover how to deploy a MERN stack with the IBM Developer Tools CLI, as well as native commands such as `kubectl` for Kubernetes and `cf` for Cloud Foundry.

In this blog post we will:

* Explain what a MERN stack is,
* Summarize a Cloud Native approach to development, and
* Introduce IBM Cloud Starter Kits

## What is MERN?

MERN stands for MongoDB, Express.js, React and Node.js. MongoDB is a popular database for web apps. Express.js is a popular web framework in JavaScript. React is a popular JavaScript library for web UIs. Node.js is a JavaScript runtime for server-side development. It is highly scalable and responds to requests quickly and thus commonly used when building web apps, IoT solutions, and low CPU intensive applications.

You may have heard of the MEAN stack, a popular technology stack for building web apps. MERN is the same, but with React instead of Angular since React is quickly gaining interest as the library created and used by Facebook.

## What is Cloud Native?

The cloud native approach to development and deployment of applications is one that takes full advantage of the characteristics of the cloud computing environment. It’s a journey that not only requires changes to the processes and workflows, but also requires a modern cloud platform built with the technology and tools to support this new approach.

Cloud native has four core pillars:

* **Microservices architecture**: Microservices, as an architecture, describes an approach to building complex applications out of smaller independent services. These services are single-purpose, with a small business capability scope. They communicate with other services using lightweight APIs and language-agnostic protocols. Microservices are built, operated, scaled, and evolved independently by teams that specialize in the business function that the microservice serves.
* **Containerization**: Containers technology offers a lightweight, highly portable virtualization solution. As compared to previous generation virtualization technology, containers are orders of magnitude faster to provision and are much more efficient in resource utilization. You don’t need containers to build microservices, but containers enable you to reliably port your application from your workstation to different cloud environments without any manual code intervention.
* **Automating with DevOps**: Teams work collaboratively using tools to automate the build, deployment, and management of apps. This approach reduces disjointed hand-offs and manual processes that can introduce human error and downtime.
* **Agile transformation**: Enterprise cloud transformation must evolve to a true multi-cloud, cloud-native development paradigm to enable market disruption. Being cloud native requires people, process, and technological transformation to be successful and truly take a sustainable leap forward.

## What is a starter kit?

Starter Kits make it very easy to following a Cloud Native programming model that uses IBM's best practices for app development. You'll see things like test cases, health check, and metrics in every starter kit. If you click on "Build from Starter kit" at the top of the Code Pattern, you'll also be able to dynamically provision Cloud services. Those services will be automatically initialized in your generated application. Add a managed MongoDB service, a Watson service, or automatic tests in a customized DevOps pipeline.

### Why use Starter Kits on IBM Cloud for your next Cloud Native app?

Starter kits are designed to get you up and running FAST. In just a few clicks you'll be able to get a cloud native web app running on different environments like Cloud Foundry or Kubernetes, the choice is yours. Starter Kits ensure that no matter the technology stack you choose, you'll follow IBM's best practices.

IBM Cloud has over 15 starter kits just for web apps. We cover MERN, MEAN, Python, Swift, and Java. In addition to web apps, IBM Cloud also provides starter kits for Apple mobile development, Watson, and finance.

![web-starter-kits]({{'/images/web-starter-kits.png'}})

## Get started quickly with your Cloud Native application

* Try the code pattern out. Check it out by going directly to our [GitHub repo](https://github.com/IBM-Cloud/MERN-app). The code pattern will walk the user through ...

* Keep an eye on [IBM Code](https://developer.ibm.com/code/patterns/) to see new content coming out daily!
