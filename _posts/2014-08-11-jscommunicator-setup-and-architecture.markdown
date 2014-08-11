---
layout: post
title:  "JSCommunicator - Setup and Architecture"
date:   2014-08-11 15:06:52
categories: tech
---

*Preface*

*During Google Summer of Code 2014, I got to work on the Debian WebRTC portal under the mentorship of [Daniel Pocock](http://danielpocock.com/). Now I had every intention of meticulously documenting my progress at each step of development in this blog, but I was a bit late in getting the blog up and running. I'll now be publishing a series of posts to recap all I've done during GSoC. Better late than never.*

**Intro**

[JSCommunicator](http://jscommunicator.org) is a SIP communication tool developed in HTML and JavaScript. The code was designed to make integrating JSCommunicator with a website or web app as simple as possible. It's quite easy, really. However, I do think a more detailed explanation on how to set things up and a guide to the JSCommunicator architecture could be of use, particularly for those wanting to modify the code in any way. 

**Setup**

To begin, please [fork the JSCommunicator repo](https://github.com/opentelecoms-org/jscommunicator.git). 

If you are new to git, feel free to follow the steps in section "Setup" and "Clone" in [this post]({% post_url 2014-07-17-contribute-a-jscommunicator-translation %}).

If you read the README file (which you always should), you'll see that JSCommunicator needs a SIP proxy that supports SIP over Websockets transport. Some options are:

* [repro from reSIProcate](http://www.resiprocate.org)

* [Kamailio](http://www.kamailio.org)

I didn't find a tutorial for Kamailio setup, I did find one for [repro setup](http://www.rtcquickstart.org/sip-proxy-installation/repro). And bonus, [here](http://danielpocock.com/get-webrtc-going-faster) you have a great tutorial on how to setup and configure your SIP proxy AND your TURN server.

In your project's root directory, you'll see a file named **config-sample.js**. Make a copy of that file named **config.js**. The **config-sample.js** file has comments that are very instructive. In sum, the only thing you *have* to modify is the [**turn_servers**](https://github.com/opentelecoms-org/jscommunicator/blob/master/config-sample.js#L11-L13) property and the [**websocket**](https://github.com/opentelecoms-org/jscommunicator/blob/master/config-sample.js#L16-L20) property. In my case, **debrtc.org** was the domain registered for testing my project, so my config file has:

{% highlight js %}
turn_servers: [
	{ server:"turn:debrtc.org" }     
],
{% endhighlight %}

Note that unlike the sample, I didn't set a username and password so SIP credentials will be used. 

Now fill in the websocket property -- here we use [sip5060.net](http://www.sip5060.net/).

{% highlight js %}
websocket: {
    servers: 'wss://ws.sip5060.net',
    connection_recovery_min_interval: 2,
    connection_recovery_max_interval: 30,
  },
{% endhighlight %}

I'm pretty sure you can set the connection_recovery properties to whatever you like. Everything else is optional. If you set the [**user**](https://github.com/opentelecoms-org/jscommunicator/blob/master/config-sample.js#L27-L34) property, specifically **display_name** and **uri**, that will be used to fill in the Login form and takes preference over any 'Remember me' data. If you also set **sip_auth_password**, JSCommunicator will automatically log in. 

All the other properties are for other optional functionalities and are well explained.

You'll need some third-party javascript that is not included in the JSCommunicator git repo. Namely jQuery version 1.4 or higher and ArbiterJS version 1.0. Download jQuery [here](http://jquery.com/download/) and ArbiterJS [here](http://arbiterjs.com/) and place the .js files in your project's root directory. Do make sure that you are including the correct filename in your html file. For example, in [**phone-dev.shtml**](https://github.com/opentelecoms-org/jscommunicator/blob/master/phone-dev.shtml#L6), a file named **jquery.js** is included. The file you downloaded will likely have version numbers in it's file name. So rename the downloaded file or change the content of src in your includes. This is uber trivial, but I've made the mistake several times.

You'll also need JsSIP.js which can be downloaded [here](http://jssip.net/download/). Same naming care must be taken as is the case for jQuery and ArbiterJS. The recently added Instant Messaging component and some of the new features need [jQuery-UI](http://jqueryui.com/download/) - so far version 1.11.* is known to work. From the downloaded .zip all you need is the jquery-ui-*.*.*.js file and the jquery-ui-*.*.*.css file, also to be placed in the project's root directory. If you'll be using the internationalization support you'll also need [jquery.i18n.properties.js](https://github.com/jquery-i18n-properties/jquery-i18n-properties/blob/master/jquery.i18n.properties.js).

To try out JSCommunicator, deploy the website locally by copying your project directory to the apache document root directory (provided you are using [apache](http://www.apache.org/), which is a good idea.). You'll likely have to restart your server before this works. Now the demo .shtml pages only have a header with all the necessary includes, then a Server Side Include for the body of the page, with all the JSCommunicator html. The html content is in the file **jscommunicator.inc**. You can [enable SSI](http://httpd.apache.org/docs/2.2/howto/ssi.html) on apache, OR you can simply copy and paste the content of **jscommunicator.inc** into phone-dev.shmtl. Start up apache, open a browser and navigate to **localhost/jscommunicator/phone-dev.shmtl** and you should see:
![jscommunicatorRaw](/assets/jscommunicatorRaw.jpg)

*Actually, pending updates to JSCommunicator, you should see a brand new UI! But all the core stuff will be the same.*




