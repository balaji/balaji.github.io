---
layout: microblog
date: 2025-01-13T23:42:37+01:00
tags:
  - helloworld
gist: >-
  I wanted a home feed for this blog, similar to social media feeds and yet keep the blog format for longform text. So I created two layouts, one called the "microblog" layout which doesn't have body but a yaml parameter that I called "gist". The other is the regular "post" layout from Jekyll. Turns out any post with only "gist" shows up empty in the RSS feed. Like, who would've thought!<br><br>One solution is to duplicate the gist content to body. But I'm quickly finding out is cumbersome. Other solution is to use body but apply character limit only to microblogs, as it should've been. 
---
