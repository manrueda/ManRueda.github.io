---
layout: post
title: Minificates AngularJS with less worries
date: '2015-11-20 00:18:42'
tags:
- npm
- dependecies
- github
- productivity
- javascript
- angularjs
- minify
- injection
---

## The pain!
We all, the web devs mostly at least, know the fears and how careful we must to be when we code to avoid error in the minified version of the code.

You forget a semicolon and half of a day goes into an endless search of the point of error in the code. With time and practice, this half day can be reduce to minutes haha, but not all the developers have that experience and less when must work with teams with different expertise.

To that, there is no so much to do... keep being careful! (There are linters and stuff...)

## A new angle of pain

With AngularJS and his dependency injection, a new things came to bother us.

The most widespread way of use the DI is the simplest way but this it is impossible to minify.
Lets see this example:
```language-javascript
angular.module('SuperModule').controller('AwesomeCrtl', function($scope, $timeout) {
});
```
This code is clean and clear but there is a problem. Angular, to use this mode of DI, parse the parameters and use the names to import the services, values, etc.
When you minify the code, the names are replaced with letters and that names have no meaning to angular.

To perform a minification to the code you must use the DI with one of the following method.

#### Dependencies Array
This way it's the most spreaded in tutorials and blogs. It's simple to understand, but it's hard to read and follow when there is a lot of injections.
The idea is to put the dependencies's names as string in the array and then the function.
```language-javascript
angular.module('SuperModule').controller('AwesomeCrtl', ['$scope', '$timeout', function($scope, $timeout) {
}]);
``` 
#### The $inject
This method use an specific property of the controller's function to specify, as an array of string, the names of the dependencies. Works with the same fundaments of the dependencies array, but it's most clean.
```language-javascript
var AwesomeCrtl = function($scope, $timeout) {
};
AwesomeCrtl.$inject = ['$scope', '$timeout'];
angular.module('SuperModule').controller('AwesomeCrtl', AwesomeCrtl);
``` 

## Share the pain
When you have to manage a work team with different expertise, as i mentioned before, this kind of thing can create a problem. Some developers forget this rules and send code to CI that breaks everything.

My first attempt to resolve that, was an awareness meeting of the subject. Worked for some but others keep breaking the CI environment.

#### My (almost) solution
Last week the solution came to me in a JS news feed. A module, [ng-annotate](https://github.com/olov/ng-annotate) is a processor of AngularJS code that searches for no secure DI functions and replace it with the dependencies array method.

This module doesn't solve all the problems, can detect a lot of DI examples but there are some very weird ones that escape from it.
Still is an excellent idea implement it in your development flow.

There is plugins for the most common task runners and asset pipeline:

* Grunt: [grunt-ng-annotate](https://www.npmjs.com/package/grunt-ng-annotate)
* Gulp: [gulp-ng-annotate](https://www.npmjs.com/package/gulp-ng-annotate/)
* Broccoli: [broccoli-ng-annotate](https://www.npmjs.com/package/broccoli-ng-annotate)

There is more... check the GitHub page of ng-annotate.

## A gift to test it
This week i made a very small implementation of this module with a browserified version of it.
You can put you Angular code and view how will be the DI output of ng-annotate.

#### Check it in : [ng-annotate-online](http://manrueda.github.io/ng-annotate-online/)