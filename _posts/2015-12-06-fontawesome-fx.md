---
layout: post
title:  "FontawesomeFX"
date:   2015-12-06 21:00:00
categories: introduction
tags: [icons, UI]
description: Coming from web-development, you'll most likely know iconic fonts as for example Bootstrap's Glyphicons or Fontawesome. This articles demonstrates how include Fontawesome in JavaFX applications.
---

Coming from web-development, you'll most likely know iconic fonts as for example Bootstrap's Glyphicons or Fontawesome and there's even more of them. They help a lot in creating appealing UIs. Naturally, I thought it'd be helpful if I could use them in JavaFX as well, after all they're just fonts. Changing the font of a control isn't hard and reading a custom font from META-INF is not rocket science either, but still...

## FontawesomeFX

With [FontawesomeFX](https://bitbucket.org/Jerady/fontawesomefx) using Fontawesome in JavaFX becomes awesomely simple. It provides you with an enumeration of all available icons. The enum values match the icon names that you can find on the [fontawesome icon page](https://fortawesome.github.io/Font-Awesome/icons/), for example `FontAwesomeIcon.SEARCH` for [fa-search](http://fortawesome.github.io/Font-Awesome/icon/search/). FontawesomeFX comes with additional icon sets that work in the same way:

 - [FontAwesome 4.5.0](https://fortawesome.github.io/Font-Awesome/) (FontAwesomeIcon.XYZ)
 - [WeatherIcons 2.0](http://erikflowers.github.io/weather-icons/) (WeatherIcon.XYZ)
 - [Material Icons](https://www.google.com/design/icons/) (MaterialIcon.XYZ)
 - [Material Design Font 1.1.70 ](https://materialdesignicons.com/) (MaterialDesignIcon.XYZ)
 - [Octicons 3.3.0](https://octicons.github.com/) (OctIcon.XYZ)

### Including FontawesomeFX with maven

FontawesomeFX is available from maven central, currently in version 8.7:

{% highlight xml %}
<dependency>
  <groupId>de.jensd</groupId>
  <artifactId>fontawesomefx</artifactId>
  <version>8.7</version>
</dependency>
{% endhighlight %}


### Integration in Scene Builder

Jens Deters provides a [video tutorial](https://youtu.be/UM05r6QsSbQ) on how to integrate FontawesomeFX in Scene Builder.

### Using an icon as caption

You can use the `GlyphsDude` to set the caption of a control, for example:

{% highlight java %}
import de.jensd.fx.glyphs.GlyphsDude;
import de.jensd.fx.glyphs.fontawesome.FontAwesomeIcon;

[...]

Text icon = GlyphsDude.createIcon(FontAwesomeIcon.CHEVRON_CIRCLE_UP, "1.5em");
icon.setFill(Color.GREEN);
new Label().setGraphic(icon);
{% endhighlight %}

This works on `javafx.scene.control.Labeled`, which includes among others Button, Checkbox, Menu, MenuItem and even cells of TreeTableView. The size is a string because it is written directly as CSS attribute, thus enabling you to use px or em as sizing units.

## Conclusion

With not even 500kb FontawesomeFX is an extremely helpful addition to nearly every program and is very easy to use.