---
layout: post
title: Realtime metrics using bitmaps, Redis, and PHP
modified:
categories: blog
excerpt:
tags: []
image:
  feature:
date: 2014-08-08T15:39:55-04:00
---
<p>Today, I came across this awesome post on the Spool blog&mdash;<a href="http://blog.getspool.com/2011/11/29/fast-easy-realtime-metrics-using-redis-bitmaps/" target="_blank">Fast, easy, realtime metrics using Redis bitmaps</a>. And I immediately wanted to build something in PHP. You should read the article to get the background of what I am trying to do here.</p>
<p>First, Java has native a Bitset library. No such luck in PHP. So, after scouring, I found that PECL has <a href="http://pecl.php.net/package/Bitset" target="_blank">one</a>. But it wasn&#39;t really complete and did not have an important function to calculate the cardinality of the resultant bitset (bitset_count()). So I compiled one from the a library I found elsewhere on Github. The code seemed to be fairly updated and had exactly what was need. It can be downloaded from <a href="https://github.com/snapbill/php-bitset" target="_blank">here</a>. Follow the instructions in the README to install the Bitset library. You might have to add the extension to your php.ini file manually:</p>
<bash>
extension=bitset.so
</bash>
<p>Next, use a PHP-Redis client of <a href="http://redis.io/clients/" target="_blank">your choice</a>. I prefer <a href="https://github.com/nrk/predis" target="_blank">Predis</a>.</p>
<p>Here's the rest of the code sample:</p>
<php>
<?php
	//Check if the bitset extension is available:
	if (!extension_loaded("bitset")) {
		print "<font color='red'>[Fail]</font> Eep! The PECL Bitset extension is not installed/enabled.<br />";
		exit;
	}
		
	//Start processing
	print "<font color='green'>[OK]</font> PECL Bitset extension found!<br />";

	//Load Predis
	require "Predis/lib/Predis/Autoloader.php";
	Predis\Autoloader::register();
	$client = new \Predis\Client();
	print "<font color='green'>[OK]</font> Predis loaded ok!<br />";

	//Set the bitset
	//Since this is a test, I will use a timestamp to create a unique Redis key so that the ouput produces is the same every time
	$ts = date('U');
	$key = "pageviews:homepage:$ts";
	$client->setbit($key, 50, 1);
	$client->setbit($key, 45, 1);
	$client->setbit($key, 3, 1);
	$client->setbit($key, 17, 1);
	
	//Get the bitset
	$pageCountBitset = $client->get($key);

	//Delete the bitset to avoid cluttering your instance
	$client->del($key);

	print "<br />";
	print "The raw bitset: " . bitset_to_string($pageCountBitset) . "<br/>";
	print "Cardinality value: " . bitset_count($pageCountBitset) . " --> This is your number of pageviews<br/>";
?>
</php>
<p>This is, of course, a test and you wouldn't normally use this to track page views real time. But it's just an indication of the vast array of problems you can solve with Redis.</p>