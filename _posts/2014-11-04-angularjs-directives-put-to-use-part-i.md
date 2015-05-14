---
layout: post
title:  "AngularJS directives put to use - Part 1"
date:   2014-11-10 21:00:00
categories: HTML javascript AngularJS
---

#### Introduction
[AngularJS][1] is a JavaScript framework that greatly assists in building rich client-side web applications. It pushes techniques
well known among backend developers to the frontend, for instance dependency injection (DI) or expression binding (more precisely two-way-binding).

Another truly helpful element of AngularJS are [directives[2]]. You can think of directives as your own means of extending HTML. Or consider it as a means
to reduce repetitive code in HTML and therefore making it more readable and improving maintainability.

This blog entry assumes a basic knowledge of AngularJS' controller concept and how to bind to bind a value of the controller with {{}}.

#### A Nav-Bar with Bootstrap

##### Initial Situation
For a little app I currently develop, I copied the basic nav bar example from the Bootstrap example and extended it with a some behaviour:

<pre brush="html">
&lt;li class="dropdown" ng-class="{
    active: isActive('/categories') || isActive('/fortune') ||
    isActive('/entry-report') || isActive('/savings') || isActive('/audit')
    }"&gt;

    &lt;a href="#" class="dropdown-toggle" data-toggle="dropdown"&gt;
        &lt;span class="glyphicon glyphicon-stats"&gt;&lt;/span&gt; Reports &lt;b class="caret"&gt;&lt;/b&gt;
    &lt;/a&gt;
    &lt;ul class="dropdown-menu"&gt;
        &lt;li ng-class="{active: isActive('/categories')}"&gt;
            &lt;a href="#/categories"&gt;Categories&lt;/a&gt;
        &lt;/li&gt;

        &lt;li ng-class="{active: isActive('/entry-report')}"&gt;
            &lt;a href="#/entry-report"&gt;Entries&lt;/a&gt;
        &lt;/li&gt;

        &lt;li ng-class="{active: isActive('/savings')}"&gt;
            &lt;a href="#/savings"&gt;Savings&lt;/a&gt;
        &lt;/li&gt;

        &lt;li ng-class="{active: isActive('/fortune')}"&gt;
            &lt;a href="#/fortune"&gt;Fortune&lt;/a&gt;
        &lt;/li&gt;

        &lt;li ng-class="{active: isActive('/audit')}"&gt;
            &lt;a href="#/audit"&gt;Audit&lt;/a&gt;
        &lt;/li&gt;

    &lt;/ul&gt;
&lt;/li&gt;
</pre>

`ng-class` is used to add a CSS classes to the element if the specified condition is met. `isActive` is a method of the corresponding
AngularJS controller, which simply returns `true` if the currently active URI of the application ends with the provided string. This way an item is
highlighted if the respective link is displayed.

As you can see, there is some repetition if you want to add another entry to the nav bar. You always have to specify the URI for `isActive` and the
`href` attribute. Additionally every nav bar entry mostly looks the same, except for the URI and the title of the link.

There is a better way: an AngularJS directive helps us to remove the repetition with both of the above mentioned problems.

##### The solution

We create a directive:

<pre brush="javascript">
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
</pre>

<figure style="float: right; margin: 5px 0 5px 15px; text-align: center">
    <img src="/img/angular/folderStructure.png" >
    <figcaption>Folder Structure</figcaption>
</figure>

This is the template, that the directives links to:

<pre brush="html">
&lt;li&gt;
    &lt;a &gt;{{title}}&lt;/a&gt;
&lt;/li&gt;
</pre>

Here are the core explanations

 * the name of the directive is `navEntry`, which means it can be used as `&lt;nav-entry&gt;` in the HTML. This is Angulars standard way of using directives.
 * we depend on `$location` being injected (for checking which URI is currently active)
 * the function `link(scope, element, attrs)` is executed once for each actual usage (instance) of the directive
      * the link of our navigation entry is set with `setSafeLink()` to the value of the path attribute and the title of the title attribute of the directive instance
      * a listener, which triggers when the URI changes, is registered
      * if the URI changes, we check whether the currently active URI matches the path attribute of the directive instance (which governs whether or not the navigation entry is active)
 * `restrict: 'E'`: the directive is used like a separate element
 * `templateUrl`: the path of content of the directive
 * `scope`:
         * this defines an own scope for each directive instance, isolated from the scope of the controller actually being responsible (isolate scope)
         * path: '@' the directive element shall have an attribute path, which value is mapped to the scope property path `(&lt;nav-bar path="/mypath"&gt;)`
         * shorthand notation for `path: '@path'`
 * `replace: true`: means that the directive usage in the HTML is replaced by content of the template of the
        directive when building the final DOM

Actually I wanted to set the `href` attribute in the template, but AngularJS has some security mechanism that prevents us from using something like

<pre brush="html">
    &lt;a href="#{{path}}"&gt;...&lt;/a&gt;
</pre>

That's why setSafeLink() adds href instead.

##### The Result
        
<pre brush="html">
&lt;li class="dropdown" ng-class="{
    active: isActive('/categories') || isActive('/fortune') ||
    isActive('/entry-report') || isActive('/savings') || isActive('/audit')
    }"&gt;

    &lt;a href="#" class="dropdown-toggle" data-toggle="dropdown"&gt;
        &lt;span class="glyphicon glyphicon-stats"&gt;&lt;/span&gt; Reports &lt;b class="caret"&gt;&lt;/b&gt;
    &lt;/a&gt;
    &lt;ul class="dropdown-menu"&gt;

        &lt;nav-entry path="/categories" title="Category Overview"&gt;&lt;/nav-entry&gt;

        &lt;nav-entry path="/category" title="Category"&gt;&lt;/nav-entry&gt;

        &lt;nav-entry path="/entry-report" title="Entries"&gt;&lt;/nav-entry&gt;

        &lt;nav-entry path="/savings" title="Savings"&gt;&lt;/nav-entry&gt;

        &lt;nav-entry path="/fortune" title="Fortune"&gt;&lt;/nav-entry&gt;

        &lt;nav-entry path="/audit" title="Balance Audit"&gt;&lt;/nav-entry&gt;

    &lt;/ul&gt;
&lt;/li&gt;
</pre>

#### Conclusion

This is a lot less code than in the initial setup and adding a new navigation entry is pretty easy now. We removed the duplication of the URI string, which is a
little less error prone. Moreover, we now could the change the template in one place instead of touching all navigation entries, which makes it DRY.

The idea could even be taken a little further. A potential next step is to make the whole dropdown element a separate directive, which could lift the need to
separately list each `isActive('/...')` branch for the contained `&lt;nav-bar&gt;`.

##### Outlook
Stay tuned for another example of what you can do with directives in part 2. Next time we'll have a look into how to format currency values.

[1]: https://angularjs.org/
[2]: https://docs.angularjs.org/guide/directive
