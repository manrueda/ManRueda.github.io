---
layout: post
title: Keep you NPM dependecies updated
date: '2015-10-09 22:46:23'
tags:
- npm
- nodejs
- updates
- dependecies
- git
- github
- hooks
- greenkeeper
---

A few days ago, surfing on the web, i saw a site of a service that called my attention and made me think "that's a great idea".

The idea was... "don't worry about update your npm's dependencies, we do..".

The service that i'm talking about is **[greenkeeper](http://greenkeeper.io/)**.

This service, periodically, will analyze the `package.json` of a GitHub repo, check for dependencies's updates and make a Pull Request with the changes. It's simple but powerful.
If you have a good set of hooks for validation, like a **travis/jenkings** builder, you can check if the new version of the dependencies breaks your code.

Greenkeeper provide three [plans](http://greenkeeper.io/#plans), one is free!!

* Open Source (FREE):
 * Unlimited public repos
 * Public queue (might take a while to update)
* Individual ($14/month):
 * Unlimited privates repos
 * Faster queue
 * Online support
* Organisations ($50-$90/month):
 * 20-50 privates repos
 * Fastest queue
 * Online support

About the plan, using the free one for now, the response time of the queue is really good. In a few (2-4) minutes i had the pull request in my repo, so for now the time is not a problem. Perhaps when the service has more demand this time increases, i hope not!

Now the implementation, there is no need of extra instructions. The **Getting started** of the official site is more than necessary. You only need to install a global NPM package, authorize greenkeeper on GitHub and then have fun.

There is only one thing about this service that not convince me at a 100%, when the service updates the `package.json` versions, removes all the [semver](http://semver.org/) symbols and puts the raw version. This is not totally wrong, because the service will update when detects a new version. But is a little detail that does not convince me completely.

In short, it is a very good alternative for those who forgets about the updates and ends with old and obsolete projects.
