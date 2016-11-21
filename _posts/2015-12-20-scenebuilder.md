---
layout: post
title:  "FMXL with Scene Builder: filling the stage"
date:   2015-12-14 21:00:00
categories: introduction
tags: [UI, Scene Builder, introduction]
description: -- todo --
---

In the previous article I explained Stages and Scenes of JavaFX applications. However, when it to came to filling the stage with life I retorted to using dummy content. This article explains
how to visually design the actual content of a Stage with FXML and Scene Builder.

## JavaFX Markup Language

I won't go into the details of FXML in this article (I'll reserve this for a later article'), because I feel it is quite intuitive when reading it. On the other hand, for producing FXML I introduce you to the Scene Builder below. You can
find the [official introduction on Oracle's website](http://docs.oracle.com/javafx/2/api/javafx/fxml/doc-files/introduction_to_fxml.html).

FXML essentially is like XML and can be used to create the *scenegraph* (i. e. the hierarchy of controls) without writing single line of code. This helps in separating GUI and business logic. There's
three major things you need to know about FXML to get started, but FXML can do a lot more.

### Using a controller for your view

Normally your view will consist of a container control (listed under *Container* in Scene Builder ) used to define in which way its children are laid out. For example


### Binding controls and listeners to your controller

### Loading FXML into the view

## Getting started with Scene Builder

Originally the Scene Builder was maintained by Oracle, but recently Gloun took responsibility for maintaining Scene Builder and continue to provide as open source. In case you're still using and old version, you should upgrade to Gloun's version of Scene Builder.

### Integrating Scene Builder with your IDE

Eclipse, Netbeans and IDEA each provide a setting to

<<default location in win 10>>

### Previewing

### CSS Analyzer

### Sample Controller Skeleton

### Using custom controls