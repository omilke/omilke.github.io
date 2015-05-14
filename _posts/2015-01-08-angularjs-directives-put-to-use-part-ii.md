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
First I came up with a very primitive approach. Wherever I displayed currency values, I used <a href="https://docs.angularjs.org/api/ng/directive/ngClass#!">ng-class</a> to
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


[1]: html/javascript/angularjs/2014/11/10/angularjs-directives-put-to-use-part-i/
[2]: https://docs.angularjs.org/api/ng/filter/currency#!













### Introduction
[AngularJS][1] is a JavaScript framework that greatly assists in building rich client-side web applications. It pushes techniques
well known among backend developers to the frontend, for instance dependency injection (DI) or expression binding (more precisely two-way-binding).

Another truly helpful element of AngularJS are [directives][2]. You can think of directives as your own means of extending HTML. Or consider it as a means
to reduce repetitive code in HTML and therefore making it more readable and improving maintainability.

This blog entry assumes a basic knowledge of AngularJS' controller concept and how to bind to bind a value of the controller with {{}}.

### A Nav-Bar with Bootstrap

#### Initial Situation
For a little app I currently develop, I copied the basic nav bar example from the Bootstrap example and extended it with a some behaviour:

{% highlight HTML %}
<li class="dropdown" ng-class="{
    active: isActive('/categories') || isActive('/fortune') ||
    isActive('/entry-report') || isActive('/savings') || isActive('/audit')
    }">

    <a href="#" class="dropdown-toggle" data-toggle="dropdown">
        <span class="glyphicon glyphicon-stats"></span> Reports <b class="caret"></b>
    </a>
    <ul class="dropdown-menu">
        <li ng-class="{active: isActive('/categories')}">
            <a href="#/categories">Categories</a>
        </li>

        <li ng-class="{active: isActive('/entry-report')}">
            <a href="#/entry-report">Entries</a>
        </li>

        <li ng-class="{active: isActive('/savings')}">
            <a href="#/savings">Savings</a>
        </li>

        <li ng-class="{active: isActive('/fortune')}">
            <a href="#/fortune">Fortune</a>
        </li>

        <li ng-class="{active: isActive('/audit')}">
            <a href="#/audit">Audit</a>
        </li>

    </ul>
</li>
{% endhighlight %}

`ng-class` is used to add a CSS classes to the element if the specified condition is met. `isActive` is a method of the corresponding
AngularJS controller, which simply returns `true` if the currently active URI of the application ends with the provided string. This way an item is
highlighted if the respective link is displayed.

As you can see, there is some repetition if you want to add another entry to the nav bar. You always have to specify the URI for `isActive` and the
`href` attribute. Additionally every nav bar entry mostly looks the same, except for the URI and the title of the link.

There is a better way: an AngularJS directive helps us to remove the repetition with both of the above mentioned problems.

#### The solution

We create a directive:

{% highlight Javascript %}
var myDirectives = angular.module('myApp.directives', []);

myDirectives.directive('navEntry', ['$location', function ($location) {

    function link(scope, element, attrs) {

        function setSafeLink(element) {

            var link = element.find('a');
            link.attr('href', '#' + attrs.path);
        }

        setSafeLink(element);

        scope.$watch(function () {
            return $location.path();
        }, function (newLocation) {

            if (attrs.path === newLocation) {
                element.addClass('active');
            } else {
                element.removeClass('active');
            }
        });
    }

    return {
        replace: true,
        restrict: 'E',
        scope: {
            path: '@',
            title: '@'
        },
        templateUrl: '/js/directives/navEntry/nav-entry.html',
        link: link
    };


}]);
{% endhighlight %}

<figure class="right" style="margin: 5px 0 5px 15px;">
    <img src="/img/angular/folderStructure.png" >
    <figcaption>Folder Structure</figcaption>
</figure>

This is the template, that the directives links to:

{% highlight HTML %}
<li>
    <a >{{title}}</a>
</li>
{% endhighlight %}

Here are the core explanations

 * the name of the directive is `navEntry`, which means it can be used as `<nav-entry>` in the HTML. This is Angulars standard way of using directives.
 * we depend on `$location` being injected (for checking which URI is currently active)
 * the function `link(scope, element, attrs)` is executed once for each actual usage (instance) of the directive
      * the link of our navigation entry is set with `setSafeLink()` to the value of the path attribute and the title of the title attribute of the directive instance
      * a listener, which triggers when the URI changes, is registered
      * if the URI changes, we check whether the currently active URI matches the path attribute of the directive instance (which governs whether or not the navigation entry is active)
 * `restrict: 'E'`: the directive is used like a separate element
 * `templateUrl`: the path of content of the directive
 * `scope`:
      * this defines an own scope for each directive instance, isolated from the scope of the controller actually being responsible (isolate scope)
      * path: '@' the directive element shall have an attribute path, which value is mapped to the scope property path `(<nav-bar path="/mypath">)` 
      * shorthand notation for `path: '@path'`
 * `replace: true`: means that the directive usage in the HTML is replaced by content of the template of the directive when building the final DOM

Actually I wanted to set the `href` attribute in the template, but AngularJS has some security mechanism that prevents us from using something like

{% highlight HTML %}
    <a href="#{{path}}">...</a>
{% endhighlight %}

That's why setSafeLink() adds href instead.

#### The Result
        
{% highlight HTML %}
<li class="dropdown" ng-class="{
    active: isActive('/categories') || isActive('/fortune') ||
    isActive('/entry-report') || isActive('/savings') || isActive('/audit')
    }">

    <a href="#" class="dropdown-toggle" data-toggle="dropdown">
        <span class="glyphicon glyphicon-stats"></span> Reports <b class="caret"></b>
    </a>
    <ul class="dropdown-menu">

        <nav-entry path="/categories" title="Category Overview"></nav-entry>

        <nav-entry path="/category" title="Category"></nav-entry>

        <nav-entry path="/entry-report" title="Entries"></nav-entry>

        <nav-entry path="/savings" title="Savings"></nav-entry>

        <nav-entry path="/fortune" title="Fortune"></nav-entry>

        <nav-entry path="/audit" title="Balance Audit"></nav-entry>

    </ul>
</li>
{% endhighlight %}

### Conclusion

This is a lot less code than in the initial setup and adding a new navigation entry is pretty easy now. We removed the duplication of the URI string, which is a
little less error prone. Moreover, we now could the change the template in one place instead of touching all navigation entries, which makes it DRY.

The idea could even be taken a little further. A potential next step is to make the whole dropdown element a separate directive, which could lift the need to
separately list each `isActive('/...')` branch for the contained `<nav-bar>`.

#### Outlook
Stay tuned for another example of what you can do with directives in part 2. Next time we'll have a look into how to format currency values.

[1]: https://angularjs.org/
[2]: https://docs.angularjs.org/guide/directive
