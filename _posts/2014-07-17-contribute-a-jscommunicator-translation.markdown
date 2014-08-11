---
layout: post
title:  "Contribute a JSCommunicator Translation"
date:   2014-07-17 8:06:52
categories: tutorial
---

To those who don't know, JSCommunicator is a SIP communication tool developed in HTML and JavaScript, currently supporting audio/video calls and SIP SIMPLE instant messaging. We've recently added i18n internationalization support for English, Portuguese, Spanish, French and now Hebrew. We would like to have [support for other languages](https://github.com/opentelecoms-org/jscommunicator/issues/5) and it's a pretty straightforward process to add a translation, so even if you do not have a tech background you can contribute!

**Setup**

First, [create and account in Github](https://github.com/). Github is a hosting service for git repositories and open source projects get to use Github for FREE! As a result, many open source project repositories are hosted on Github, including but not limited to [JSCommunicator](https://github.com/opentelecoms-org/jscommunicator). 


Once you have created your git account, follow steps 1-4 in the section Setting up git listed in [Github's setup tutorial](https://help.github.com/articles/set-up-git). This will help you install git and configure anything you need. 

**Fork**

Now fork the JSCommunicator repo. I really like the word 'fork'. Great term. If this is your first fork, know that this will make a copy of the JSCommunicator repo and all its respective code to your Github account. Here's how we go about it:

1. Sign in to [Github](http://github.com)

2. Browse to https://github.com/opentelecoms-org/jscommunicator.git

3. Somewhere around the upper left corner, you will see a button labeled 'Fork'. Click that. 

![fork](/assets/fork.jpg)

You should now have your own JSCommunicator repository on your Github account. On your main page select the tab 'Repositories'. It should be the first on the list. The url will be https://github.com/YOUR-USERNAME/jscommunicator. My Github username is JLouback, so my jscommunicator repo can be found at https://github.com/JLouback/jscommunicator.

**Clone**

Now you should download all that code to your local machine so you can edit it. On your JSCommunicator page somewhere around the lower right corner you'll see a field entitled HTTPS clone URL:

![clone](/assets/cloneURL.jpg)

I don't know if this is correct, but you can just copy the URL on your browser too. I do that.

Now on your terminal, navigate to the directory you'd like to place your repository and enter
{% highlight ruby %}
git clone https://github.com/YOUR-USERNAME/jscommunicator
{% endhighlight %}

The current directory should now have a folder named 'jscommunicator' and in it is all the JSCommunicator code. 

**Switch branches**

The JSCommunicator repo has a branch for the i18n support. A branch is basically a copy of the code that you can modify without changing the original code. It's great for adding new features, once everything is done and tested you can add the new feature to the original code. A more detailed explanation of branches can be found [here](https://help.github.com/articles/creating-and-deleting-branches-within-your-repository).

On the terminal, enter 
{% highlight ruby %}
cd jscommunicator
git branch -a
{% endhighlight %}

This will list all the branches in the repository, both local and remote. There should be a branch named 'remotes/origin/i18n-support'. Switch to this branch by entering
{% highlight ruby %}
git checkout --track origin/i18n-support
{% endhighlight %}
This will create a local branch named i18n-support with all the contents of the remote i18n-branch in https://github.com/opentelecoms-org/jscommunicator.

**Create a .properties file**

Now your jscommunicator directory will have some new files for the internationalization functionality. Among them you should find an INTERNATIONALIZATION_README file with instructions for adding a JSCommunicator translation. It's a less detailed version of this post, but it's worth a read in case I've missed something here. 

Go to the directory 'internationalization'. You'll see a few .properties files with different language codes. Choose the language of your preference for the translation base and make a copy of it in the internationalization directory. Your copy should be named 'Messages_LANGUAGECODE.properties'. For example, the language code for german is 'de', so the german file should be named 'Messages_de.properties'. If you're not sure about the code for your language, please check out this [list of ISO 639-1 language codes](http://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) so you're using the same code as (most) browsers. 

The .properties file is list of key-value pairs, a word or a few words joined by '_' which is the key, the equals sign '=' then a word or phrase which is the value. Do *not* change anything to the *left* of the equals sign. Translate what is on the *right* of the equals sign. 

**Modify available_languages.xml**

Go back to the root directory (jscommunicator) and open the file named 'available_languages.ruby'. This .ruby has a series of language elements. At the end of the last '</language>' add a new language element with your language display name and code, copying the previous languge elements. You actually can put your language element anwhere, as long as it's after the '<list>' and before the </list>, as well as not cutting into any of the other language elements. Your display name should be the name of the language in its native language, for example for a french translation we'll put 'Français' (I think). And the code is the code we used for the .properties file. Here's how it should look:
{% highlight ruby %}
<language>
    <display>Deutsch</display>
    <code>de</code>
</language>
{% endhighlight %}

Save your changes and voila!

**Test your work**

Once you've made a .properties file, JSCommunicator will automatically load your translation if you've set your browser preference to that language. If your browser preference is french, it will load the Messages_fr.properties. If your browser preference is a language we don't have a translation for (let's say german), JScommunicator will load the default Messages.properties file which is in english. 

The available_languages.ruby is used to build a language selection menu. If you add a language element without a corresponding .properties file, JSCommunicator will throw a JS error and load the default (english) translation. The same happens if you use different language codes in the .properties file name and the available_languages.ruby. The error won't disturb your use of JSCommunicator, it just won't load the language you selected. No harm, no foul. But if you want others to use your translation, do take care to do this correctly.

If you read the INTERNATIONALIZATION_README, you will have seen that we need the jquery.i18n.properties.js file that's included in the .html pages. You can download that code [here]( https://code.google.com/p/jquery-i18n-properties/). Be sure to place it in the jscommunicator directory. This is only necessary if you want to see your addition at work, you don't need this file to contribute your translation. There are other 3rd party code dependencies to run JSCommunicator too as you may have noticed. Just creating the .properties file and adding a language element to available_languages.ruby, if done correctly, is enough to make a pull request. 

**Commit**

Here's the part where we load all your local changes to your remote repository. In the terminal, navigate to the root directory (jscommunicator) and enter
{% highlight ruby %}
git add internationalization/Messages_de.properties
git add available_languages.ruby
{% endhighlight %}
Of course, please replace 'Messages_de.properties' with the name of your new .properties file. And mind you, we don't have a german translation yet!

Then enter
{% highlight ruby %}
git commit -m "German translation"
{% endhighlight %}
You can enter whatever you'd like in the " " part, it's a good idea to explain what language you are adding. Now we can push the changes:
{% highlight ruby %}
git push -u origin i18n-support
{% endhighlight %}
You should now see the changes you made in your Github account page at github.com/YOUR_USER/jscommunicator. The message you put in double quotations (" ") in the commit will be beside each modified file. This push also creates a new branch in your jscommunicator repository, now you will have a 'master' and a 'i18n-support' branch.

**Pull**

Now it's time to share your translation with everyone else if you feel so inclined. Your version of JSCommunicator has a new translation but not the official version. First navigate to you jscommunicator repo at github.com/YOUR_USER/jscommunicator and make sure you are on the i18n-branch as that will contain your changes.

![branches](/assets/branches.jpg)

Click the green button directly to the left of the branch drop down menu shown above. This will create a pull request to add your new code to the official jscommunicator code. Once you click that button, you should see this:

![pull](/assets/pull.jpg)

Make sure that the base repo (the first on the line above the green Create pull request button) is opentelecoms-org:**i18n-support** and not opentelecoms-org:**master** or another branch name. If it is, just click 'Edit' on the right and you'll be able to select the branch from a dropdown like so:

![pullEdit](/assets/pullEdit.jpg)

If first repo is opentelecoms-org:i18n-support and the second is YOUR_USER:i18n-support and you've verified (scroll down) that there are 2 files changed which are your new .properties file and the added element to the available_languages.xml, go ahead and press create pull request. It'll open a window with a text box you can add an explanation to with one final 'Create pull request' button. Click that and you're done! You've kindly contributed with a translation for JSCommunicator! 
