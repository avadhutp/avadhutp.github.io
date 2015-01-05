---
layout: post
title: Web application as microservice in Java
modified:
categories: blog
excerpt:
tags: []
image:
  feature: blog/vms.jpg
---
Motivation:

1. I want to run multiple web services (applications, REST APIs, etc.) on shared infrastructure.
1. However, if one of these goes down it should not take down the entire web container with them, thereby disabling other services.
1. Solution: embed the container, which in my case is Jetty.

The setup we run:

**Step 1:** Have your `main` start an instance of the embedded Jetty on a port number you desire. In our organisation, we use ports `8XXX`.

{% github_sample_ref /avadhutp/web-app-microservice/master/src/main/java/io/github/avadhutp/webapprecipe/generic/StartUp.java %}
{% highlight java %}
{% github_sample /avadhutp/web-app-microservice/master/src/main/java/io/github/avadhutp/webapprecipe/generic/StartUp.java 25 45 %}
{% endhighlight %}

**Step 2:** Package it as a fat JAR.
{% highlight bash %}
mvn -V clean deploy
{% endhighlight %}

**Step 3:** Run/Deploy the JAR on your infrastructure box. We use Fabric.

**Step 4:** Use Apache vhost configuration to supply custom (non-port-number-based) URLs.
{% highlight bash %}
<VirtualHost *:80>
  ServerName custom.sample.url
  ProxyPass / http://localhost:8090/
  ProxyPassReverse / http://localhost:8090/
</VirtualHost>

<VirtualHost *:443>
  RequestHeader set X-Forwarded-Proto "https"
  RequestHeader set X-Forwarded-Port "443"
  
  ServerName custom.sample.url
  ProxyPass / http://localhost:8092/
  ProxyPassReverse / http://localhost:8092/
</VirtualHost>
{% endhighlight %}

Here's a link to the entire codebase on Github—[web-app-microservice](https://github.com/avadhutp/web-app-microservice).

I like this approach as I think it is simple, clean, and relatively easy to understand and implement. However, if you spot any problems or have any suggestions, please leave a comment. (ツ)