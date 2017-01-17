---
layout: post
title: Typescript Everywhere
date: '2016-02-25 22:44:00'
tags:
- grunt
- angularjs
- mongodb
- typescript
- gulp
- angular2
- orm
- iridium
---

Time to time i get a little bit bored with the tools or task that i'm doing, because of that i like to start new projects with tools/languages/methodologies/etc that i'm not use to use.
That helps me to clear my mind and think in another stuff to get rid of the blocks in my head or the boredom.

But well, that is not the idea of this entry, even the name has no relation, the idea is what i have experienced in my last projects of this kind.

## The project

The project was something very simple, a site to manage all my home chores, status on the house and share it with my girlfriend. Even i was thinking to connect it with some Arduino project, but that is another story.

How i told before, the idea it's to pick technologies that i'm not use to use.
So i pick [Typescript](http://www.typescriptlang.org/) as a language, i use it but not very much. 
[Gulp](http://gulpjs.com/) as a building tool, i'm a [Grunt](http://gruntjs.com/) user mostly. 
[MongoDB](https://www.mongodb.org/) for the database, that not new for me, i used it a lot but not recently and i want to go back and use it again. But i change my ORM, before i used [Mongoose](http://mongoosejs.com/), but for this time i found [Iridium](https://github.com/SierraSoftworks/Iridium), a ORM built with [Typescript](http://www.typescriptlang.org/), so was great to avoid to have to found the Typings of one more package.

But that was only for the back end (and building task), about the front end i want to challenge myself with more changes and chose [Angular 2](https://angular.io/) to keep with the Typescript initiative you know...

## The meaning of the title

The project was great, i learned a bunch of new thing about each part. And made me better in the things that i already know.

But in a moment i notice that i was using the same language at back end and front end, and remember my beginning with NodeJS when the motto was "JS on the server" and "use the same language for all".

And yeah... i know... i did not discover America with this phrase. But was very revealing to me and gave me the change to test some things.

For example i reuse the same Interfaces used in the ORM in the Angular services. So, when i change something typescript told me instantaneously, at the back end and front end. 
This is not a very good idea in a very large project, i'm coupling back and front very heavily. But for this small project was a good experiment.