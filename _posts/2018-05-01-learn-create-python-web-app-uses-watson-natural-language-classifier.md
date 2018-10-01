---
title: "Learn how to create a Python web app that uses Watson Natural Language Classifier"
excerpt: "My first take at using Watson's Natural Language Classifier"
tags: 
  - software
  - ibm
  - ibmcloud
  - watson
  - natural language
  - python
---

_Originally posted on [https://developer.ibm.com/code/2018/05/01/learn-create-python-web-app-uses-watson-natural-language-classifier](https://developer.ibm.com/code/2018/05/01/learn-create-python-web-app-uses-watson-natural-language-classifier)_

Natural language classification (NLC) is related to the field of [natural language processing (NLP)](https://en.wikipedia.org/wiki/Natural-language_processing), and other related technologies include natural language understanding (NLU) and natural language generation (NLG). IBM Watson™ provides a cloud-based service aptly called [Watson Natural Language Classifier](https://www.ibm.com/watson/services/natural-language-classifier/), which allows a developer to create classifiers for text, and using cognitive computing techniques, it will return the best matching predefined classifier.

The easiest way I found to wrap my head about NLC was to use the sample data in the [Getting started tutorial](https://console.bluemix.net/docs/services/natural-language-classifier/getting-started.html). You can watch a walk-through [video](https://www.youtube.com/watch?v=SUj826ybCdU) of that tutorial, which uses Watson Natural Language Classifier to classify questions about weather. In the tutorial, we have a small dataset of text input to act as our training data. The training data includes sentences we train as part of the `weather` class, such as "How is the weather outside?" or "Is it snowing?" and sentences we train as part of the `temperature` class, such as "What's the temperature outside?" or "Is it cold outside?" Using even a small dataset, we can see fairly high accuracy to questions not in our training data - questions that involve "blizzard" or "rain," for instance.

We recently created a "[Classify ICD-10 data with Watson](http://developer.ibm.com/code/patterns/classify-icd-10-data-with-watson)" code pattern to take things a bit further. We created a small Python-based web app and used a much larger dataset - specifically, the [ICD-10 dataset](https://en.wikipedia.org/wiki/ICD-10), which classifies medical diagnoses to an ICD-10 designation. Check out the code in our [GitHub repo](https://github.com/IBM/nlc-icd10-classifier) - fork it, clone it, modify it to fit your use case.

It's not hard to imagine a scenario where text classification could be useful. Classifying email, tweets, or posts as spam or malicious is an easy-to-understand example. Perhaps we could use Watson Natural Language Classifier to look up FAQs or other documents (like ICD-10).

To learn more about Watson Natural Language Classifier, check out the following resources:

* [Sample apps that also use NLC](https://console.bluemix.net/docs/services/natural-language-classifier/sample-applications.html#sample-apps)
* [5 Things to Know About Watson Natural Language Classifier](https://www.ibm.com/developerworks/community/blogs/5things/entry/5_things_to_know_about_Watson_Natural_Language_Classifier?lang=en)
* [Watson Natural Language Classifier Best Practices](https://medium.com/ibm-watson/watson-natural-language-classifier-fb66206be6de)
* [Watson Natural Language Classifier APIs](https://www.ibm.com/watson/developercloud/natural-language-classifier/api/v1/curl.html?curl)

Happy hacking!
