---
layout: post
title:  "Effectively using oh-my-zsh in Windows Subsystem for Linux (WSL)"
date:   2021-02-27 09:00:00
categories: practical, tools
tags: [Maven, UI, JavaFX]
description: Using Sass provides some convenience for JavaFX and this article will get you started.
author: olli
excerpt_separator: <!--more-->
comments: true
disqus_id: 18
---

I have not published an article for a really, really long time, because I felt I had nothing to share. Now I do have the feeling I have just little bit worth sharing. 

## Using Sass with JavaFX and maven

In case you work with JavaFX you'll most likely use CSS for styling your application. When doing so, chances are you might enjoy some of the benefits Sass can bring to the table, especially in regard to IDE integration.

<!--more-->

### My relation to UI development

I've really never been into web development. But I just enjoyed learning new things and there has been time when this curiosity has been about CSS. 

I assume there might be newer cool kids around in the meantime, but back then I figured out that Sass provides a lot of convenience over plain CSS and that just stuck in my head ever since. Luckily, that convenience also applies to using CSS in JavaFX.     

### Why Sass?

As is often the case, I had a new idea to toy around with private JavaFX project. Having some CSS styling in my application, I thought a little more efficiency and convenience would serve me well, because I just like both.

* using variables, mainly for re-using the same colors (but essentially everything else, as well)
* nesting declarations instead of repeating the selectors 

Actually, there's more to [Sass](https://sass-lang.com/guide) - but this is what I wanted to achieve. 

IntelliJ IDEA is my favorite IDE, and it's just amazing. However, re-using the same colors as kind of variable never really worked that well with JavaFX-CSS. It has, however, great support for Sass :)

### Using Sass in with JavaFX

Once you're willing to use Sass with JavaFX, you'll likely find [an awesome article by Andres Almiray](https://andresalmiray.com/sassy-javafx/) providing just what you need to know. Kudos for saving me a lot of research :)

Sadly, I noticed some weird behaviour with the referenced sass-maven-plugin in the latest-version (3.7.2).  

## Reliable build with sass-maven-plugin

### digging into the cause

The sass-maven-plugin is a built detection for whether the Sass has changed over the last compilation of the processed css. This actually is a good thing. However, I found out that sometimes my CSS is missing in the final build leading to a NPE when trying to read styles and applying to my scene. After some debugging, I found out that this seems to tied to the way the plugin detects whether to compile the CSS anew or just skip it. The plugin unfortunately always considered a rebuild unnecessary when run with `mvn clean package`, which explains the missing CSS in the final artifact.

One thing I didn't research is, whether this happens on Windows only.

### what worked for me

After some experimenting I went with the following configuration ([complete picture on GitHub](https://github.com/omilke/bankingFX/blob/master/pom.xml)):

    <plugin>
                <groupId>nl.geodienstencentrum.maven</groupId>
                <artifactId>sass-maven-plugin</artifactId>
                <version>3.7.2</version>
                <configuration>
                    <!-- using dedicated folders seems to work best with rebuild-required detection, at least when being a bit early to the party (i. e. in generate-resource phase) -->
                    <sassSourceDirectory>${basedir}/src/main/resources/sass/</sassSourceDirectory>
                    <destination>${project.build.outputDirectory}/compiled-css/</destination>
                </configuration>
                <executions>
                    <execution>
                        <id>update-stylesheets</id>
                        <phase>generate-resources</phase>
                        <goals>
                            <goal>update-stylesheets</goal>
                        </goals>
                    </execution>
                </executions>
    </plugin>

There's two main aspects here:

* using dedicated directories for input and output
* applying the plugin just before the other resources are processed in `process-resources` phase.

### Conclusion

Using Sass makes my development just a little easier and can easily be integrated into the maven-build with the information in the article. Using the configuration above works just fine for me. 

I also added IDEA's run configurations for just compiling the Sass and adjusted my run configurations for running my application to also compile the Sass beforehand. You can look them up on [GitHub](https://github.com/omilke/bankingFX/tree/master/.idea/runConfigurations)