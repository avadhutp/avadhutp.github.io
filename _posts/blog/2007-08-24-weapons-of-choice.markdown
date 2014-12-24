---
layout: post
title: Weapons of choice
modified:
categories: blog
excerpt:
tags: []
image:
  feature:
---
Most of my development happens in Drupal, PHP, jQuery, MySQL, PostGreSQL, and all good things associated. Until recently, I used to swear by separate PHP and Apache installations. But managing updates for everything involved—Apache, PHP, jQuery, Drupal—separately had become a real bitch: the innumerable backups, migrations, and restorations.

So when Drupal 6 was released, I was thrilled with the new <strong><em>Update </em></strong>module. And it was time I found a server-PHP stack that could support production-scale deployments. Enter ZendCore. The installer not only allows you to use Zend's own PHP (with FastCGI) and Apache Web Server (ZendCoreApache) but also plays nice with your existing installations. Buy a subscription with Zend, and they will take care of updating Apache and PHP for you too. It also allows you to download and install database servers of your choice—MySQL, DB2, etc.

ZendCore also comes bundled with Zend Framework, which provides an extremely good set of PHP functionalities. I have only gotten as far as <strong><em>Lucene search</em></strong>, but things look very promising.

Drupal runs like a peach on this stack, which is most important for me at the moment, and it supports my other custom Web-apps developed using the Zend framework.

I am almost tempted to shell out on ZendPlatform, which is an enormously feature-rich Web-app server. It handles PHP, Java, and HTTP monitoring, supports session clustering (yummy!), and comes with a really cool Download Server that frees up Apache of monotonous tasks involving huge downloads so that it can handle some of the more serious stuff.

However, I still could not get myself to switch to Zend Studio. Or maybe I just refuse to let go of my beloved Komodo IDE. What? What was that? Dreamweaver? Who? The one that pisses memory all over the place? Yeah I dumped it with DotNetNuke. :P

<blockquote>The worst excuse I have heard anyone give me for still sticking to Dreamweaver is the Code/Design view. Please, it is a joke. Any standards-aware Web designer will tell you that. It still gloats about its Table layout like it were the 90s. Besides, how long does it take to write a line of CSS, alt+tab your way to your browser, and press F5? Also, what's with the no-Linux-option? Not even a binary? What is taking Adobe so long? Hurry the fuck up. And don't even get me started with the abyssmal FTP editing. I have actually shut my PC mid-day and gone home coz' of it. Free FTP programs have no issues handling huge files. Then why does it have to be such a task for a $500 software to do it? If my server supports long sessions, then so should Dreamweaver. To top it all off, it is way overpriced. Did you see the CS3 release—not only is it godforsakenly bloated (it was consuming 20% CPU just idling), it also expects me to pay Adobe almost three times more than what it would charge my American friends.</blockquote>

The thing I like the most about Zend is that none of the components are prone to lock-ins. Use whichever tool you want, it will still behave with the etiquette expected out of good-natured software. Want to use ZendCore's PHP with your own Apache deployment? It's OK. ZendCore won't hold any grudges. Some other people do need to take a lesson from here. Can you hear me Microsoft? Just because I use Exchange does not mean that I would want to use Office Communication Server. Let me connect to other software!

So, the list would proceed as follows:
<ul>
	<li>Webserver-PHP stack: ZendCore</li>
	<li>PHP frameworks: Zend Framework, Drupal (I use it as one :))</li>
	<li>Javascript: jQuery, Prototype</li>
	<li>IDE: Komodo</li>
	<li>Database server: MySQL, MSSQL, PostGreSQL</li>
	<li>Mail server: Apache James</li>
	<li>OS: Does it even matter?</li>
</ul>