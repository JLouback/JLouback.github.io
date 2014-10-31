---
layout: post
title:  "JSCommunicator at xTupleCon 2014"
date:   2014-10-30 10:06:52
categories: tech
---

Two weeks ago I left NYC for a day trip to Norfolk, Virginia, to attend [xTupleCon 2014](http://xtuple.com/xtuple-conference-2014). For those who don't know, [xTuple](http://xtuple.com) is an incredibly reknown open source Enterprise Resource Planning (ERP) software. If you've been following this blog, you might recall that during my participation in the Google Summer of Code 2014, I wrote a beta JSCommunicator extension for xTuple (to see how I went about doing that, look up [Kickstarting the JSCommunicator-xTuple extension]({% post_url 2014-07-25-kickstarting-the-jscommunicator-xtuple-extension %})).

Now, I wasn't flying down to Virginia on the eve of my first grad school midterms (gasp!) for fun - although I will admit, I enjoyed myself a fair amount. My GSoC mentor, [Daniel Pocock](http://danielpocock.com) was giving a talk about WebRTC and JSCommunicator at xTupleCon and invited me to participate. So that was the first perk of going to xTupleCon, I got to finally meet my GSoC mentor in person! 

During the presentation, Daniel provided a high level expanation of WebRTC and how it works. WebRTC (Web Real Time Communications) enables real time transmission of audio, video and data streams through browser-to-browser applications. This is one of the many perks of WebRTC; it doesn't require installation of plugins, making its use less vulnerable to security breaches. Websockets are used for the signalling channel and the SIP or XMPP can be used as the signaling protocol - in JSCommunicator, we used SIP. What was also done in JSCommunicator and can be done in other applications is to use a library to implement the signaling and SIP stack. JSCommunicator uses (and I highly reccomend) [JsSIP](http://jssip.com). 

The basic SIP architecture is comprised of peers and a SIP Proxy Server that supports SIP over Websockets transport:

![sipArch](/assets/sipArch.png)

If you have users behind NAT, you'll also need a TURN server for relay:

![sipArch2](/assets/sipArch2.png)

In both cases, setup is not too difficult, particularly if using [reSIProcate](http://www.resiprocate.org) which offers both the SIP proxy and the TURN server. Daniel Pocock has an excellent post on [how to setup and configure your SIP proxy and TURN server](http://danielpocock.com/get-webrtc-going-faster).

With regard to JSCommunicator, it is a generic telephone in HTML5 which can easily be embedded in any web site or web app. Almost every aspect of JSCommunicator is easily customizable. More about JSCommunicator setup and architecture is detailed in a [previous post](% post_url 2014-08-11-).

The JSCommunicator-xTuple extension can be installed in xTuple as an npm package (xtuple-jscommunicator). It is still at a very beta - or even pre-beta - stage and there are various limitations; the configuration must be hard-coded and dialing is done manually as opposed to clicking on a contact. Some of these 'limitations' are features are on the wish list for future work. For example, some ideas for the next version of the extension are a click to dial from CRM records functionality and to bring up CRM records for an incomming call. Additionaly, the SIP proxy could be automatically installed with the xTuple server installation if desired. 

We closed the presentation with a live demo during which I made a video call from a JSCommunicator instance embedded in [freephonebox.net](http://freephonebox.net) to the JSCommunicator xTuple extension running on Daniel's laptop. Despite the occasionally iffy hotel Wifi, the demo was a hit - at one point I even left the conference room and took a quick walk around the hotel and invited other xTupleCon attendees to say hello to those in the room. The audience's reception was more enthusiastic than I anticipated, giving way to a pretty extensive Q&A session. It's great to see more and more people interested in WebRTC, I can't emphasize enough what a useful and versatile tool it is.

Here's an 'action shot' of part of the xTuple WebRTC presentation:
![xtuple](/assets/xtuple.png)



