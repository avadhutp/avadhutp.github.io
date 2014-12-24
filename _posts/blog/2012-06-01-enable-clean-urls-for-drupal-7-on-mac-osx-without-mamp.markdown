---
layout: post
title: Enable clean URLs for Drupal 7 on Mac OSX without MAMP
modified:
categories: blog
excerpt:
tags: []
image:
  feature:
date: 2014-08-08T15:39:55-04:00
---
<p>Having worked with Ubuntu all of my live, you could understand my frustrations when I tried to get clean urls working for Drupal 7 on Mac OS X. Searching in forums all over, most comments directed me to MAMP. Now don't get me wrong&mdash;MAMP is a fantastic piece of work. But I wanted to install and control all the components on my Desktop myself. Call me anal.<p>
<p>What I describe below is the surest and easiest way of ensuring that clean URLs work in your Mac environment.<p>
<ol>
<li>In OS X Lion, you do not need to worry about enabling mod_rewrite&mdash;it's enabled by default.</li>
<li>Open <bash>/etc/apache2/httpd.conf</bash> and look for
<bash highlight=5>
# AllowOverride controls what directives may be placed in .htaccess files.
# It can be "All", "None", or any combination of the keywords:
#   Options FileInfo AuthConfig Limit
#
AllowOverride None
</bash>
Change Line #5 above to
<bash start=5>
AllowOverride All
</bash>
</li>
<li>Open <bash>/etc/apache2/users/[username].conf</bash> and look for
<bash>
AllowOverride None
</bash>
Change it to
<bash>
AllowOverride All
</bash>
</li>
<li>Restart your Apache by going to System Preferences => Sharing. Toggle the checkbox on Web Sharing.</li>
<li>In the folder where you've installed Drupal (most probably located at <bash>/Users/[username]/Sites/drupal</bash>), edit the <bash>.htaccess</bash> file. Look for
<bash>
# RewriteBase /
</bash>
Uncomment this line and modify it to
<bash>
RewriteBase /~[username]/drupal
</bash>
</ol>
<p>That should do it!</p>