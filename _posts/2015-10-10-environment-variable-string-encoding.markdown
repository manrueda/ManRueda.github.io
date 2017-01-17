---
layout: post
title: Environment variable string encoding
date: '2015-10-10 20:59:00'
tags:
- ghost-tag
- nodejs
- string
- buffer
---

When i started to set up this blog (based in ghost), i put a bunch of sensitive data in environment variables in the heroku configuration.

The problem was a key of google drive API, the content is multi-line and can't make it work in one line. When i put the new line character in the string `\n`, this wasn't interpreted by node.

The only solution that i found for this problem was the next one, convert the string to a buffer and then to string again. 

<script src="https://gist.github.com/ManRueda/5dc1570f128a557ac345.js"></script>
