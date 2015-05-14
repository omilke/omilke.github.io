---
layout: post
title:  "AngularJS directives put to use - Part 2"
date:   2015-01-08 21:00:00
categories: HTML javascript AngularJS
---

### Introduction
This is the follow up of [AngularJS directives put to use - part 1][1]. In the last article I showed how to build a directive for Bootstrap's navigation bar. While this hugely saves redundant code and totally simplifies adding new navigation entries, I'll show something even more practical and useful in this article.

I am developing an application for tracking ones expenses. Thus it is only natural to display a lot of currency values. AngularJS provides a fantastic means to display bare
    currency values: the [currency filter][2]. With the introduction of AngularJS 1.3 Google also improved the currency filter. Now you can specify how many decimal digits you want to be displayed.

This greatly helps to uniformly display currency values (especially depending on your locale) but still the result is a bare string. When tracking ones
    expenses there are naturally not only expenses, i. e . negative values. To spend money you first have to earn it. Therefore, you also have positive values. I wanted them to
    be displayed differently. Negative values should have a red font, positive values should have a green font.

### The primitive approach
First I came up with a very primitive approach. Wherever I displayed currency values, I used [ng-class][3] to
    decide with class to apply depending on the value to display.

{% highlight HTML %}
<td ng-class="{'positive': x.amount >= 0,
                  'negative': x.amount < 0}">{{x.amount| currency }}</td>
{% endhighlight %}

This starts to get annoying pretty soon when all over your application markup like this pops up. I thought there had to be a more convenient way - and there was! You can guess it:
    directives to the rescue.

I build simple directive that encapsulated the distinction between positive and negative values and the application of respective css class.

### A little more sophisticated approach
{% highlight javascript %}
var myDirectives = angular.module('myApp.directives', []);

myDirectives.directive('accentuatedCurrency', function($filter) {

    function link(scope, element, attrs) {

        var text = attrs.accentuatedCurrency;

        var value = parseFloat(text);
        if (value < 0) {
            element.addClass('negative');
        } else {
            element.addClass('positive');
        }

        var formatted = $filter('currency')(value);
        element.text(formatted);

    }

    return {
        restrict: 'A',
        link: link
    };
});
{% endhighlight %}

With this directive I was able to simply use the following snippet:

{% highlight HTML %}
<td accentuated-currency="{{x.amount}}"></td>
{% endhighlight %}

This made formatting currency values depending on the signum a breeze. However, some time later I had to learn that this directive wasn't as useful as I thought. This approach
effectively circumvented the two-way-databinding AngularJS provides, because the decision which css class is to be applied was only performed once, namely when the page is initially
constructed. Subsequent changed to `x.amount` where completely ignored.

### The final approach

Similarly to the approach of the last article I had to introduce a listener, which reacts to the change of `x.amount` and updates the applied css class respectively.

{% highlight javascript %}
var myDirectives = angular.module('myApp.directives', []);

myDirectives.directive('accentuatedCurrency', function($filter) {

        function link(scope, element, attrs) {

            function setAccentuatedValue(changed) {
                var value = parseFloat(changed);

                if (value < 0) {
                    element.addClass('negative');
                    element.removeClass('positive');
                } else {
                    element.addClass('positive');
                    element.removeClass('negative');
                }

                var formatted = $filter('currency')(value);
                element.text(formatted);
            }

            scope.$watch(attrs.accentuatedCurrency, function (value) {
                setAccentuatedValue(value);
            });

        }

        return {
            restrict: 'A',
            link: link
        };
}]);
{% endhighlight %}

Now the linking function registers a listener to be executed upon a value change instead of immediately applying a css class. That way even if the value bound `x.amount`
    changes, the format changes with it.

As with the last example, using this directive saves redundant code and makes your markup a little more DRY. I hope I could demonstrate another practical use of directives.


[1]: /html/javascript/angularjs/2014/11/10/angularjs-directives-put-to-use-part-i/
[2]: https://docs.angularjs.org/api/ng/filter/currency#!
[3]: https://docs.angularjs.org/api/ng/directive/ngClass#!
