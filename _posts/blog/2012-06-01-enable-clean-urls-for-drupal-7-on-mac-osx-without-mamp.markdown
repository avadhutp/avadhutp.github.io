---
layout: post
title: Enable clean URLs for Drupal 7 on Mac OSX without MAMP
modified:
categories: blog
excerpt:
tags: []
image:
  feature:
---
Having worked with Ubuntu all of my live, you could understand my frustrations when I tried to get clean urls working for Drupal 7 on Mac OS X. Searching in forums all over, most comments directed me to MAMP. Now don't get me wrong—MAMP is a fantastic piece of work. But I wanted to install and control all the components on my Desktop myself. Call me anal.

What I describe below is the surest and easiest way of ensuring that clean URLs work in your Mac environment.

1. In OS X Lion, you do not need to worry about enabling mod_rewrite—it's enabled by default.
1. Open `/etc/apache2/httpd.conf` and look for
{% highlight bash %}
# AllowOverride controls what directives may be placed in .htaccess files.
# It can be "All", "None", or any combination of the keywords:
#   Options FileInfo AuthConfig Limit
#
AllowOverride None
{% endhighlight %}

3. Change Line #5 above to `AllowOverride All`
4. Open `/etc/apache2/users/[username].conf` and look for `AllowOverride None`. Change it to `AllowOverride All`.
5. Restart your Apache by going to `System Preferences => Sharing`. Toggle the checkbox on `Web Sharing`.
6. In the folder where you've installed Drupal (most probably located at `/Users/[username]/Sites/drupal`), edit the `.htaccess` file. Look for `# RewriteBase /`. Uncomment this line and modify it to `RewriteBase /~[username]/drupal`.

That should do it!
