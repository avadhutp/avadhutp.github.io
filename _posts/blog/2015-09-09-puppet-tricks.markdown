---
layout: post
title: Puppet tricks
modified:
categories: blog
excerpt:
tags: []
image:
  feature: blog/puppet.jpg
---
I use Puppet at [work](engineeringblog.yelp.com), but not often enough. So I keep forgetting some basic, but important things. Hence, this post: to document that which I easily forget.

* Is there a easy way to find classes assigned to a node in puppet other than grepping through the code?

  Check `/var/lib/puppet/state/classes.txt`.

* Can I see with what parameters these classes were intialized?

  Check `/var/lib/puppet/client_data/catalog/'hostname -f'.json`. You might have to use jq to read it though.
