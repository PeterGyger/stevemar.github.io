---
title: "Integrating Watson Assistant with WordPress"
excerpt: "How to integrate your Watson Assistant chatbot with WordPress"
tags: 
  - software
  - advocacy
  - watson
image:
  path: /images/watson-wp-logo.png
  thumbnail: /images/watson-wp-logo.png
---

I recently published a tutorial on [IBM Developer](https://developer.ibm.com/tutorials/how-to-integrate-your-watson-assistant-chatbot-with-wordpress/) about integrating Watson Assistant with WordPress. I wanted to give a super, brief description here on my personal site.

The gist of it is:

* We use [Docker Compose to spin up an instance of WordPress locally](https://www.hostinger.com/tutorials/run-docker-wordpress#gref) (or if you have your own WordPress site, even better).

* Install the [Watson Assistant plugin](https://wordpress.org/plugins/conversation-watson/) on your site.

   ![assistant_plugin_install]({{'/images/assistant_plugin_install.png'}})

* Create your Watson Assistant service, make a dialog, all that fun stuff (or use the sample dialog if you're just toying around).

* Configure the plugin (there are a lot of options!) to use your Watson Assistant credentials and Workspace ID

* Test it out.

   ![chatbot on wordpress]({{'/images/chatbot.png'}})
