---
layout: post
title:  "JSCommunicator 2.0 (Beta) is Live!"
date:   2014-08-14 23:06:52
categories: tech
---

This is the last week of Google Summer of Code 2014 - all good things must come to an end. To wrap things up, I've merged all my work on JSCommunicator into a new version with all the added features. You can now demo the new and improved (or at least so I hope) JSCommunicator on [rtc.debian.org](https://rtc.debian.org/)!

JSCommunicator 2.0 has an assortment of new add-ons, the most important new features are the Instant Messaging component and the internationalization support.

The UI has been reorganized but we are currently not using a skin for color scheme - will be posting about that in a bit. The idea is to have a more neutral look that can be easily customized and integrated with other web apps.

A chat session is automatically opened when you begin a call with someone - unless you already started a chat session with said someone. Sound alerts for new incoming messages are optional in the config file, visual alerts occur when an inactive chat tab receives a new message. Future work includes multiple user chat sessions and adapting the layout to a large amount of chat tabs. Currently it only handles 6. *(Should I allow more? Who chats with more than 6 people at once? 14 year old me would, but now I just can't handle that. Anyway, I welcome advice on how to go about this. Should we do infinite tabs or if not, what's the cut-off?)*

About internationalization, I'm uber proud to say we currently run in 6 languages! The 6 are English (default), Spanish, French, Portuguese, Hebrew and German. But one thing I must mention is that since I added new stuff to JSCommunicator, some of the new stuff doesn't have a translation. I took care of the Portuguese translation and Yehuda Korotkin quickly turned in the Hebrew translation, but we are still missing an update for Spanish, French and German. If you can contribute, please do. There are about 10 new labels to translate, you can fix the issue [here](https://github.com/opentelecoms-org/jscommunicator/issues/42). Or if you're short on time, shoot me an email with the translation for what's on the right side of the '=':

welcome = Welcome, 

call = Call

chat = Chat 

enter_contact = Enter contact

type_to_chat = type to chat...

start_chat = start chat

me = me

logout = Logout

no_contact = Please enter a contact.

remember_me = Remember me

I'll merge it myself but I'll be sure to add you to the authors list - or maybe I'll just take all the glory and pretend to be a polyglot.  