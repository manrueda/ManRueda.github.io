---
layout: post
title: Devtool as an IDE... it's time?
date: '2016-01-25 16:30:00'
tags:
- nodejs
- tools
- productivity
- javascript
- google
- devtool
- future
- ide
---

On the last days i have seen a lot of new stuff and implementations of the devtool of Google Chrome and customization for that, and thats keeps me thinking... This could be the time to finally join IDE and test/run tools?

Well.. to be fair there are tools that joins thats, like JetBrains Webstorm and they are awesome! but even with the super cool integration with Chrome, there is a lack... it's more like a feeling of lack of something hahha

#Devtool
From day one, the devtool of Google Chrome give us a lot of cool tools and options to be faster in our daily web debugging. With the versions came new stuff and this behavior is awesome, thats is the key to be a great tools.

#node-inspector
Then with NodeJS [Danny Coates](https://github.com/dannycoates) creates [node-inspector](https://github.com/node-inspector/node-inspector) and with that we can debug node code like web code, thats was great!
But still, there are a lack.. you need to run node-inspector aside your code, open a browser tab and some not implemented features. Anyway it's a great tool and i used it a lot of time.

#The new things
In the last few weeks some cool project came to me, extending and re implementing the devtools.

### devtool
[Devtool](https://github.com/Jam3/devtool) is like node-inspector but more independent, using [Electron](https://github.com/atom/electron/) to the visual part. Allows you to use it in many ways, REPL, debugger, renderer, etc.
Has HTML capabilities, so is not only for node. Can be used to fast web prototyping.

### Devtools Author
[Devtool Author](http://mikeking.io/devtools-author/) is something nice rather than a productivity tool, add customization to Chrome Devtools and allow you to be more confortable with the UI, you can set the style like your editor and generate a more smooth transition.

#Wrapping up
At the end the idea of this entry isn't to show all the features of this tools or all the tools (there are a lot of more out there).
The idea is to share my feeling about how those new things with dev tools could generate a very good IDE to work with.