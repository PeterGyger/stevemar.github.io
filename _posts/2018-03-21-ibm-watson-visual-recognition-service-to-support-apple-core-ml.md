---
title: "IBM’s Watson Visual Recognition service to support Apple Core ML technology"
excerpt: "Announcing that the Watson Visual Recognition service will now support Core ML"
tags:
  - software
  - advocacy
  - watson
  - swift
image:
  path: http://developer.ibm.com/code/wp-content/uploads/sites/118/2018/03/Apple_IBM_050616.png
  thumbnail: http://developer.ibm.com/code/wp-content/uploads/sites/118/2018/03/Apple_IBM_050616.png
---

_Originally posted on [https://developer.ibm.com/code/2018/03/21/ibm-watson-visual-recognition-service-to-support-apple-core-ml/](https://developer.ibm.com/code/2018/03/21/ibm-watson-visual-recognition-service-to-support-apple-core-ml/)_

_This post is co-authored by [Steve Martinelli](https://www.linkedin.com/in/stevemar/) and [Sridhar Sudarsan](https://www.linkedin.com/in/sridhar-sudarsan-278603)_

We're excited to announce that the Watson Visual Recognition service will now support Core ML. This is the latest addition to the [Apple and IBM partnership](https://www-03.ibm.com/press/us/en/pressrelease/44370.wss) that was forged nearly four years ago. The announcement is taking center stage at Think 2018, showcasing the value this new functionality brings to iOS developers worldwide. This post introduces you to Core ML and Watson Visual Recognition, and explains how to use the various assets available to you.

[![IBM and Apple logos](http://developer.ibm.com/code/wp-content/uploads/sites/118/2018/03/Apple_IBM_050616.png)](http://developer.ibm.com/code/wp-content/uploads/sites/118/2018/03/Apple_IBM_050616.png)

## Core ML

[Core ML](https://developer.apple.com/documentation/coreml), first released in iOS 11, is an Apple framework for running machine learning models locally on iOS-enabled devices, meaning it runs even when the device is offline. Existing models written in [Caffe](http://caffe.berkeleyvision.org/), [Keras](https://keras.io/), [Scikit-learn](http://scikit-learn.org/stable/) and others can be converted to Core ML.

[![Core ML logo](http://developer.ibm.com/code/wp-content/uploads/sites/118/2018/03/coreml.png)](http://developer.ibm.com/code/wp-content/uploads/sites/118/2018/03/coreml.png)

## Watson Visual Recognition

[Watson Visual Recognition](https://www.ibm.com/watson/services/visual-recognition/) is a service on IBM Cloud that enables you to quickly and accurately tag, classify, and train visual content using machine learning. It has built-in classifiers for objects such as faces and food. To get familiar with these classifiers, you can try the [online demo](https://visual-recognition-demo.ng.bluemix.net/). In addition, there's support for custom models. To create a custom model, you upload several images to the service for training. When the custom model is trained and tested, you can use it in API calls.

## Developers, get hands-on experience

We've created a code pattern, [Deploy a Core ML model with Watson Visual Recognition](https://developer.ibm.com/code/patterns/deploy-a-core-ml-model-with-watson-visual-recognition), that will help you dive into this new functionality. It includes an overview of the code, an architecture diagram and steps, a demo video, and related links, all in one spot. (There are also plenty of other [code patterns](https://developer.ibm.com/code/patterns/?cm_sp=Developer-_-Top-Nav-_-Journeys) to check out on IBM Code.)

The folks that create the various Watson SDKs are _really smart_. They were able to use the existing [Watson Swift SDK](https://github.com/watson-developer-cloud/swift-sdk) to make taking advantage of the Core ML features in Watson Visual Recognition a breeze. They've created a [Github repo](https://github.com/watson-developer-cloud/visual-recognition-coreml/) to walk users through using the SDK for this scenario.

You can start making use of the Watson Swift SDK Core ML features in just a few lines of code. Here's an example of how to classify an image with a local model:

    visualRecognition.classifyWithLocalModel(image: image, classifierIDs: [classifierId], threshold: localThreshold, failure: failure)

and here's an example of how to update a local model:

```swift
func invokeModelUpdate()
{
    let failure = { (error: Error) in
        print(error)
        let descriptError = error as NSError
        DispatchQueue.main.async {
            self.currentModelLabel.text = descriptError.code == 401 ? "Error updating model: Invalid Credentials" : "Error updating model"
            SwiftSpinner.hide()
        }
    }

    let success = {
    DispatchQueue.main.async {
        self.currentModelLabel.text = "Current Model: \(self.classifierId)"
        SwiftSpinner.hide()
    }
}

visualRecognition.updateLocalModel(classifierID: classifierId, failure: failure, success: success)
```

## Build a production-ready iOS app

At Think 2018, IBM is also announcing a new [IBM Cloud Developer Console for Apple](https://console.bluemix.net/developer/appledevelopment). Within the console are several [iOS Starter Kits](https://console.bluemix.net/developer/appledevelopment/starter-kits), which are designed to be production-ready in minutes. The [Custom Vision Model for Core ML with Watson](https://console.bluemix.net/developer/appledevelopment/starter-kits/custom-vision-model-for-core-ml-with-watson) starter kit is designed to get you up and running fast!

[![](http://developer.ibm.com/code/wp-content/uploads/sites/118/2018/03/dev_console_315.png)](http://developer.ibm.com/code/wp-content/uploads/sites/118/2018/03/dev_console_315.png)

## Should I use it in my application?

Yes, it’s a no-brainer! Core ML is optimized for iOS devices, and Watson Visual Recognition services provide a way to create custom models based on the image data you provide, using the tools. You'll quickly have your app using images more intelligently.

## Watson/Core ML buzz

The tech community loves the latest Apple/IBM collaboration. Here's what they're saying:

*   [Apple, IBM add machine learning to partnership with Watson-Core ML coupling (TechCrunch)](https://techcrunch.com/2018/03/19/apple-ibm-extend-partnership-with-watson-core-ml-coupling/)
*   [Apple and IBM unveil artificial intelligence service that Coca-Cola is testing (Fortune)](http://fortune.com/2018/03/20/apple-ibm-artificial-intelligence/)
*   [Apple & IBM combine Watson and Core ML for the smartest ever mobile apps (9to5Mac)](https://9to5mac.com/2018/03/20/apple-ibm-watson-core-ml/)
*   [Introducing IBM Watson Services for Core ML (Apple Developer)](https://developer.apple.com/news/?id=03202018a)
*   [Blog by Sridhar Sudarsan: AI Everywhere with IBM Watson and Apple Core ML](https://www.ibm.com/blogs/watson/2018/03/ai-everywhere-ibm-watson-apple-core-ml)

## References

The following are great resources to get started.

*   [Code pattern: Deploy a Core ML model with Watson Visual Recognition](http://developer.ibm.com/code/patterns/deploy-a-core-ml-model-with-watson-visual-recognition)
*   [Github: Use the Watson Swift SDK to create a custom Core ML model](https://github.com/watson-developer-cloud/visual-recognition-coreml/)
*   [Starter Kit: Custom Vision Model for Core ML with Watson](https://console.bluemix.net/developer/appledevelopment/starter-kits/custom-vision-model-for-core-ml-with-watson)
*   [iOS Starter Kits](https://console.bluemix.net/developer/appledevelopment/starter-kits)
*   [IBM Cloud Developer Console for Apple](https://console.bluemix.net/developer/appledevelopment)
