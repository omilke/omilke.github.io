---
layout: post
title:  "Stages and Scenes: unveiling the main actors"
date:   2015-12-13 19:00:00
categories: introduction
tags: [UI, Window, introduction]
description: When building JavaFX applications you will sooner or later need to create new windows. This articles provides you with all the details of creating a window and explains the concept of Stages and Scenes.
excerpt_separator: <!--more-->
---

When creating a JavaFX [application](http://docs.oracle.com/javase/8/javafx/api/javafx/application/Application.html), you need at least following parts
  - an application
  - a main [stage](http://docs.oracle.com/javase/8/javafx/api/javafx/stage/Stage.html) with a [scene](http://docs.oracle.com/javase/8/javafx/api/javafx/scene/Scene.html)
  - content to be displayed


This is usually what is provided either by the hello-world-sample or the skeleton created by your IDE and typically looks the like in this in image.

<!--more-->

![basic explanation of a JavaFX application]({{site.baseurl}}/img/fx/hello-javafx.png "Anatomy of a JavaFX application")


In this case, the VBox being inserted into the Scene and all controls within that VBox (the actual content) are said to be the *scenegraph*.

JavaFX applications can contain additional stages as the following depiction indicates.

![JavaFX application and stages]({{site.baseurl}}/img/fx/stages.png "JavaFX application and its stages")

A Stage is an implementation of [Window](http://docs.oracle.com/javase/8/javafx/api/javafx/stage/Window.html) with the alternative implementation being [PopupWindow](http://docs.oracle.com/javase/8/javafx/api/javafx/stage/PopupWindow.html), which is used for context menus or tooltips. On a side node, this also clarifies why you can put regular controls into a tooltip.

## Creating Stages

You can always add new stages to a running JavaFX application with the following code

{% highlight java %}
final Scene scene = new Scene(new VBox());
final Stage stage = new Stage();
stage.setTitle(title);
stage.setScene(scene);
stage.show();
{% endhighlight %}

This creates a stage with default look.

There actually is a restriction here, though. Always actually means always, as long as called from JavaFX Application Thread. That thread is the default thread when not manually creating new threads. I am going to cover the details of the  JavaFX Application Thread in a future article.

### Owner of a new stage

When creating a new stage, an already existing stage can become the parent of the new stage. This especially means there is no new taskbar entry and owned stages are automatically closed when its parent is closed.

{% highlight java %}
newStage.initOwner(alreadyExistingStage);
stage.show();

{% endhighlight %}

The owner must be set before the `stage.show()` and cannot be changed afterwards, which is why the method is not named like a typical setter.

### Decoration and Modality

If you want the newly created stage to look and behave differently you can adjust the [StageStyle](http://docs.oracle.com/javase/8/javafx/api/javafx/stage/StageStyle.html) and the [Modality](http://docs.oracle.com/javase/8/javafx/api/javafx/stage/Modality.html).

{% highlight java %}
final Scene scene = new Scene(new VBox());
final Stage stage = new Stage();
stage.setTitle(title);
stage.setScene(scene);

stage.initModality(Modality.APPLICATION_MODAL);
stage.initStyle(StageStyle.UNIFIED);

stage.show();
{% endhighlight %}

`Modality` defines which parts of the application remain usable (i. e. continue to receive events) after displaying the new stage

- NONE
    - the whole application continues to receive events
    - for additional windows to be used side by side
- WINDOW_MODAL
    - the parent of new stage will not receive new events
    - windows which are not parent to this stage will continue to receive events
    - can be used make an essential selection within a window
- APPLICATION_MODAL
    - the whole application stops to receive events
    - like in a dialog

The `StageStyle` provides platform specific decorations of the new window, for instance removing the minimize and maximize buttons and just displaying a close button (`StageStyle.UTILITY`). Just go ahead and try how this changes the look of your window on your platform.

Again, these flag have to be set before the stage is made visible with `show()`.

### Dialogs

Beginning with Java 8u40 Oracle provides a separate API for displaying dialogs. I am going to cover them in a future article.

##  Closing Stages

There is two identical ways to close a stage

{% highlight java %}
stage.close();

//or

stage.hide(); //inherited from Window
{% endhighlight %}

They both work the same and it is up to you decide which you prefer. You get the Window (which is the normally is the stage) of an arbitrary control with `getScene().getWindow()`, which then can be closed with `hide()`.

 {% highlight java %}
VBox vBox = new VBox();

final Scene scene = new Scene(vBox);
final Stage stage = new Stage();
stage.setTitle(title);
stage.setScene(scene);
stage.show();

//immediately close the window
vBox.getScene().getWindow().hide();
 {% endhighlight %}

### Exiting the Application

A JavaFX application by default exits when all stages of the application are closed. This behaviour can be turned off with `javafx.application.Platform.setImplicitExit(false);`, which means that the JavaFX application continues to run even after all stages are closed.

Additionally any JavaFX application can be shutdown with `javafx.application.Platform.exit()`. This enables the regular shutdown of your application. On the contrary `System.exit()` prevents the JavaFX runtime shutdown and therefore should be avoided.

## Conclusion

When creating a JavaFX application you need a subclass of `Application`, the runtime will provide your primary `Stage` and you have to create  a `Scene` and insert your content there.

The upcoming article will be about the [Scene Builder](http://gluonhq.com/open-source/scene-builder/), which is an alternative to programmatically inserting the content into your scene.



