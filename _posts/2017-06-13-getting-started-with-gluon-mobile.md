---
layout: post
title:  "Getting started with Gluon Mobile - or what I learned at Jugend Hackt Hamburg 2017"
date:   2017-06-13 22:30:00
categories: introduction
tags: [UI, Android, JavaFX, IntelliJ, introduction]
description: This article is a short summary on how to setup Gluon Mobile, which I learned at Jugend Hackt Hamburg 2017.
author: olli
---

Last weekend was "Jugend Hackt Hamburg 2017" and even though my task was to mentor the youngsters, I learned how to setup Gluon Mobile from working with a very bright and motivated participant. He came to me because he knew I like working with JavaFX and he heard about that framework, that enables developing android application with JavaFX. We made our way together to getting started with Gluon Mobile on Windows.

By the way, I wrote a [summary about Jugend Hackt Hamburg on my company's blog](https://www.triology.de/blog/hacker-wochenende-das-war-jugend-hackt-nord), which is definitely worth a read.

## About Gluon Mobile

[Gluon Mobile](http://gluonhq.com/products/mobile/) is framework that enables you to write mobile applications for Android and iOS using JavaFX. This can come in handy in case you already know how use JavaFX. There also is [an extensive developer documentation](http://docs.gluonhq.com/charm/4.3.5/).

## Prerequisites for using Gluon Mobile for android apps

You'll need an [Android SDK](http://developer.android.com/sdk/index.html#Other) (which is comes along with Android Studio), Java 8 and Gradle 2.2. After having installed Android Studio, you can chose the Platform SDKs to install. Gluon Mobile requires at least Android API Level 21 (Android 5.0). 

In order to test the application on your android phone, you'll obviously need to install the SDK matching your phone's android version. You'll also need the Android Support Library to conveniently install the application on your device.

Additionally you need enabled USB Debugging on your phone, which in turn requires you to [activate Android's Developer Mode](http://www.greenbot.com/article/2457986/how-to-enable-developer-options-on-your-android-phone-or-tablet.html).

Lastly, you can use your favorite IDE to develop, which is described on [Gluon's page](http://docs.gluonhq.com/charm/4.3.5/#_project_setup). The Gluon Plugin is not available for Android Studio, but for IntelliJ Idea Community Edition, which also supports JavaFX.

## Creating a project in IntelliJ Idea

The Gluon Plugin provides a project template for Idea utilizing a gradle build. After finishing the Gluon Plugin wizard, I was shown a dialog to define the gradle settings. However, since the project was already created, I could not properly configure the build. The solution for me was to cancel the dialog and manually re-import the existing project:
 
*File -> New -> Project from existing sources* and then select the *build.gradle* in the directory created by the wizard.

The build still needs to know where your Android SDK is located. The easiest way is to use your *%USERPROFILE%\.gradle\gradle.properties* file:

    ANDROID_HOME=C:/Users/<username>/AppData/Local/Android/sdk

Alternatively you can create a system variable with the same name / value pair.

I could not set the Android SDK path using the *build.gradle* as documented and found on [stackoverflow](https://stackoverflow.com/a/31403967).

This is it! Connect your phone and execute *other / installAndroid*. You'll then have the application installed on your phone.

Now you can use your mighty JavaFX skills to develop android applications ;-)

By the way:

This should work the same in eclipse, however eclipse seems to hide the Other category of the gradle tasks, which contains the installAndroid task. There is a solution on [stackoverflow](https://stackoverflow.com/a/40358734/2108919).

## Conclusion

Of course this could spark the typical cross-platform vs. native development discussion, but I'm confident there are better places for that discussion ;-)

As this article demonstrates, you're ready to go within a very short time. Currently I do not have any experience using Gluon Mobile. However, I have some ideas I want to realize as mobile app. After gathering some experience, I'll add a follow up article.
