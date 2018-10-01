---
title: "Create a next-generation call center with Voice Gateway"
excerpt: "Use the Voice Gateway offering on IBM Cloud Private to orchestrate Watson services and Twilio"
tags: 
  - software
  - ibm
  - ibmcloud
  - twilio
  - watson voice gateway
  - watson assistant  
---

_Originally posted on [https://developer.ibm.com/announcements/create-a-next-generation-call-center-blog](https://developer.ibm.com/announcements/create-a-next-generation-call-center-blog)_

Have you called a help desk recently only to be greeted by a primitive, robotic-sounding routing system? How was the experience? Or perhaps you own a call center? How much of your staff is spending their valuable time answering the same, repetitive questions?

There's a solution to these problems - IBM Voice Gateway and IBM Watson services.

The best part? You don't need to modify your existing infrastructure, and you don't even need to orchestrate the connection between your call center infrastructure with the Watson services on the public cloud. The IBM Voice Gateway offering can manage that orchestration for you! Voice Gateway is an offering available on IBM Cloud Private, meaning that the orchestration between services (your call center and the Watson services) can be done on-premises.

In this blog post, we will:

* Describe what the new code pattern does
* Provide a brief overview of the components used
* Explain how to get started

## What did we build?

The code pattern we built, the [source code is on GitHub](https://github.com/IBM/voice-gateway-on-icp), walks you through setting up IBM Voice Gateway, IBM Cloud Private, a few Watson services on IBM Cloud, and Twilio's Elastic SIP Trunking. After configuring all the pieces, you are able to call a phone number and talk to a virtual agent in a much more realistic manner. You can extend and reuse this solution in a production call center.

## A brief overview of the components used

As mentioned in the title, this [code pattern](/patterns/create-a-next-generation-call-center-with-voice-gateway/) uses Voice Gateway, IBM Cloud Private, and a few Watson services.

### What is IBM Voice Gateway?

[IBM Voice Gateway](https://www.ibm.com/support/knowledgecenter/en/SS4U29/welcome_voicegateway.html) provides a way to integrate a set of orchestrated Watson services with a public or private telephone network using the Session Initiation Protocol (SIP). Voice Gateway enables direct voice interactions over a telephone with a cognitive self-service agent or the ability to transcribe a phone call between a caller and an agent in real time, enabling the ability to process the conversation with analytics for real-time agent feedback. Voice Gateway can be deployed on Kubernetes, IBM Cloud Private, or IBM Cloud Kubernetes Service. You can find much more information in the [Knowledge Center](https://www.ibm.com/support/knowledgecenter/en/SS4U29/about.html).

IBM Voice Gateway also lets you use a service orchestration engine (SOE) to provide customization with APIs. This SOE sits between Watson Assistant and Voice Gateway so that you can further customize your environment with your own third-party APIs. See [their GitHub repo](https://github.com/WASdev/sample.voice.gateway) for examples. Additionally, Voice Gateway supports English, Japanese, Portuguese (Brazilian), and Spanish.

![selfserviceagent]({{'/images/selfserviceagent.png'}})

IBM Voice Gateway provides a pretty nice set of features for self-service agents:

* *Barge-in*: Callers can interrupt Watson if the utterance Watson is sending to the caller is not relevant to the context of the conversation.
* *Call transfer*: The gateway can be signaled to initiate a transfer from the Watson Assistant service by using action tags. To perform the transfer, the gateway uses a SIP REFER request as defined in section 6.1 of RFC 5589.
* *Call hang-up*: The gateway can be signaled to terminate a call from the Watson Assistant service by using an action tag.
* *Music on hold*: The gateway can play an audio file that is specified by Watson Assistant for a specified time or until processing in Watson Assistant completes.
* *SSML tagging*: Speech Synthesis Markup Language (SSML) tags are used to control how Text to Speech synthesizes utterances into audio. The gateway supports passing these tags through to the Text to Speech service when received from Watson Assistant.

... and much more

![phone-contrast]({{'/images/phone-contrast.jpg'}})

### What is IBM Cloud Private?

[IBM Cloud Private](https://www.ibm.com/cloud/private) is a Kubernetes-based container platform that can help you quickly move, modernize, and automate workloads or build new cloud-native applications. The IBM Cloud Private catalog includes containerized IBM middleware that's ready to deploy into the cloud. Containerization erases concerns about application-specific breakage points when modernizing monolithic, heritage applications. This enables you to reduce downtime by resolving a single issue without taking down the entire system. IBM also provides capabilities like Transformation Advisor and Voice Gateway to help you isolate application interdependencies and facilitate the modernization process.

### Which Watson services were used?

Simply put, we used three services for this code pattern:

* [Watson Speech to Text](https://www.ibm.com/watson/developercloud/speech-to-text.html): Converts the caller's audio into text
* [Watson Assistant](https://www.ibm.com/watson/developercloud/conversation.html): Analyzes the text, maps it to intents or capabilities, and provides a response according to a dialog
* [Watson Text to Speech](https://www.ibm.com/watson/developercloud/text-to-speech.html): Converts the response into voice audio

![robots]({{'/images/robots.jpg'}})

## How can I get started?

* Try out the [code pattern](/patterns/create-a-next-generation-call-center-with-voice-gateway/). For the source code, you can go directly to the [GitHub repo](https://github.com/IBM/voice-gateway-on-icp). The code pattern walks you through configuring all the components and provides a few sample prompts to give the sample conversation.

* Want a demo instead? Check the video on [YouTube](https://www.youtube.com/watch?v=AG3rti3yV1E).

* Keep an eye on [IBM Developer](https://developer.ibm.com/patterns/) to see new content coming out daily!
