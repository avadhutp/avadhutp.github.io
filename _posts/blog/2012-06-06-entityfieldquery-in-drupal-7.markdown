---
layout: post
title: EntityFieldQuery in Drupal 7
modified:
categories: blog
excerpt:
tags: []
image:
  feature:
---
Drupal 7 is a filled with a lot of things that fill you with delight and despair all at the same time. And EnityFieldQuery is one of them. It seems to do everything and yet, when refuses to bend to your will.

Recently, in one of my projects, the use case was that we had to display the number of PDF files the currently logged in user generated in the current month. A simple thing if I were using SQL. But I wanted so badly to do this using EntityFieldQuery. And here's what I ended up with:
{% highlight php %}
$eQuery->entityCondition('entity_type', 'file') //Set the type of entity to retrieve
->propertyCondition('uid', $user->uid) //Set the current user's UID
->propertyCondition('status', 1) //You want to retrieve only active files
->propertyCondition('filemime', 'application/pdf') //The file type is PDF
->propertyCondition('timestamp', array(mktime(0, 0, 0, date('n'), 01, date('Y')), mktime(0, 0, 0, ((date('n')) + 1), 0, date('Y'))), 'BETWEEN') //Only for the current month
->count(); //Give me only the count; redact this line if you need all FIDs

$fileUsage = $eQuery->execute();
{% endhighlight %}

Voila! I truly &hearts; EntityFieldQuery.

There's a [thread on Drupal.org of more such examples](http://drupal.org/node/916776#comment-6085986). Do have a look. I too have cross-posted this example there. Also, [the api.drupal.org page on EntityFieldQuery](http://api.drupal.org/api/drupal/includes!entity.inc/class/EntityFieldQuery/7) has some fantastic documentation—be sure to check it out.