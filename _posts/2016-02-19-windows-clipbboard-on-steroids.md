---
layout: post
title:  "Windows Clipboard on Steroids"
date:   2016-05-24 20:00:00
categories: practical, tools
tags: [Windows, Utilities]
description: Windows default clipboard is essential, though still annoyingly restrictive. Ditto ultimately improves the clipboards utility character.
author: olli
excerpt_separator: <!--more-->
---

Have you ever imagined to use your computer with a non-functional clipboard a.k.a. copy and paste? This would definitely be painful.

However, even though the basic windows clipboard is essential, the single element clipboard is still annoyingly restrictive. In his inspiring book _"The Productive Programmer"_ Neal Ford recommended to use a clipboard enhancing tool. This thought especially caught my attention and consequently I looked into finding a suitable tool for Windows.

<!--more-->

## Ditto Clipboard Manager
I've been using [Ditto Clipboard Manager](http://ditto-cp.sourceforge.net/) for some time now, and now copying this is simply amazing.

<figure class="right">
    <img src="/img/ditto/ditto-history.png" style="width: 250px;" title="Ditto Clipboard History pop-up" alt="Ditto Clipboard History pop-up">
    <figcaption>Ditto Clipboard History pop-up</figcaption>
</figure>


Ditto basically keeps a history of what was copied. *STRG + Ö* on German keyboard layout opens a pop-up showing this history as you can see in the image.

When focusing a text-input, opening this pop-up enables you to select the content to insert from the history of copied element. You can either directly select the element by pressing the corresponding number key or going through the list with the arrow keys and pressing enter. Inserting an element makes it the first element, which can still be inserted with standard *STRG + V*.

Additionally you can also filter the history, which helps in finding older entries to insert them again. For instance a setting for a git repository config file or a special command needed every now and then. I'm sure you can come up with a lot of scenarios in which this is ultimately helpful.

You can configure the expiration time of clipboard history elements and a lot more details. Just have a look at the settings, although the default settings seem sensible to me.

<figure class="right">
    <img src="/img/ditto/ditto-config.png" style="width: 450px;" title="Ditto Settings" alt="Ditto Settings">
    <figcaption>Ditto Settings</figcaption>
</figure>

The history of elements mainly enables batch copying with batch pasting, which eliminates the need to switch between windows when transferring multiple elements, saving you a lot of time.

## Caveats

Ditto by default send *STRG +V* to insert the selected element to the cursor position, which is not working in cygwin, gitbash, or putty. However, since the selected element is put the to top of the history, you can then insert the element in whatever way required, i. e. right-click the window and choose insert or use another shortcut.

By default, Ditto also keep track of each password you copied from a password manager like KeePass, thus posing a security risk. Luckily you can exclude applications from generating history elements in the settings of Ditto. Just add KeePass.exe as exclusion as you can see in the screenshot.

## Conclusion

Ditto has become my most used and essential tool for every day work. I wouldn't want to miss it and I can absolutely recommend you to use it, as well.' Moreover, "The Productive Programmer" contained some more truly helpful advice, which I am going to cover in a later article.

