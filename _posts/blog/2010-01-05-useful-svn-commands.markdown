---
layout: post
title: Useful SVN commands
modified:
categories: blog
excerpt:
tags: []
image:
  feature:
---
Often, I need to add directories and files to SVN recursively. This little bit helps a lot:

<bash>
sudo svn status | grep "^\?" | awk '{print $2}' | xargs sudo svn add
</bash>