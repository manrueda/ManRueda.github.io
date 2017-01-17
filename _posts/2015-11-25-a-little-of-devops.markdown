---
layout: post
title: A little of DevOps
date: '2015-11-25 16:33:49'
tags:
- ghost-tag
- server
- ubuntu
- linux
- devops
- google
- cloud
---

Last weekend, in the middle of the Presidential Elections here in Argentina, I decided to make some change in the blog.

Add SSL, get a better performance, etc... For that I need to move it away from Heroku (it's all ok with Heroku... what I want to test new stuff). So I started the trial period in Google Cloud, they give you **$300 to expend in 60 days**.

To create a blog or stuff like that there are a lot of 'templates'. *[Bitname](https://bitnami.com)* it's a great site with a lot of templates, you can create blogs, git repos, wikis, databases and a long etc. Also supports other cloud platforms like *[Azure](https://bitnami.com/azure)*, *[Amazon](https://app.bitnamihosting.com/?auth=true)*, *[Oracle](https://bitnami.com/oracle)*.

### Anyway... I want to do it myself!

My first fear was the security, I'm doing it right?, this port should be opened? and other questions came to me. But the community helped me, there are lots of articles and blog's entries about how to deploy everything in Google Cloud. Databases, HTTP Servers, etc.

This blog is a NodeJS application, so to expose to the internet I installed Nginx to proxy the port 80 to the node port.
Thats was super easy, a few config lines over there and it's ready!

### Security.... or Insecurities
One new thing for me, that was a little tricky, was the SSL. I'm using *[Let's Encrypt](https://letsencrypt.org/)*, this is a cool project to **provide trusted certificates for free**. Still under close beta, almost in an open beta, works great! The response time of the request are excellent!

### Some issues
This note is more a reminder to me than a possible solution for you. Currently I don't know why this happened, but I think it's good to leave it here.

In the morning of the Monday the blog stop working, the SSH connection to the server wasn't responding, nothing was working. But the instance was running!
Something struck me, the CPU was working at 150% of capacity!!

My solution was start from scratch, I only restore the virtual drive to backup the database from another instance.
