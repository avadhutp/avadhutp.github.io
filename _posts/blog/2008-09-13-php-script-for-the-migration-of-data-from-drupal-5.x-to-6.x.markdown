---
layout: post
title: PHP script for the migration of data from Drupal 5.x to 6.x
modified:
categories: blog
excerpt:
tags: []
image:
  feature:
---
Ok. Drupal 6 is probably the best thing since bread came sliced. Views 2, the new and improved CCK, and the updater—it’s like updating your anti virus—has made life so much easier. Updating Web application frameworks has never been easier.

This updater alone is incentive enough to upgrade your Drupal 4.x and 5.x installations to Drupal 6.x. Unfortunate it is then that this moat to be crossed is filled with non-upgraded module crocodiles and inexplicable PHP error sharks.

There are two approaches to converting a Drupal 5.x site to 6.x:
1. Upgrading: For this, all contrib. modules installed in your 5.x better be ported to 6.x, and also pray that Watchdog has encountered no life-threatening PHP errors. That periodic cron runs, database updates, &amp; zilch-modification of core modules were a must go without saying.
2. Migration: Setup a crisp, clean Drupal 6.x site, and painstakingly port all data from your 5.x behemoth to your 6.x kiddo. Migration can be performed either manually (pray, lord, pray if your site has more than 500 nodes) or through scripts.

Although approach 1, if it works without spewing any errors, is enough to incite a hallelujah from even the most ardent of the non-believers, we all know how rare it is. And the pre-requisites to it are just too many and too hard to fulfill.

The only solution in cases where an upgrade is not possible, then, is the use of automated migration scripts. Currently, there is no such unified migration script available. I have broken down the script I used to migrate my 5.x site to 6.x for the migration of users, simple-content-type posts, comments, taxonomy, &amp; forum posts. Migrating the theme itself (blocks, nodes, templates, etc.) would require one to get their hands dirty with PHP.

Before we begin, we need our PHP migration script to set certain variables:

{% highlight php %}
< ?php
set_time_limit(0);
//MySQL globals
$mysql_server = "localhost";//change this server for your Drupal installations
$mysql_user = "root";//Ideally, should be root
$mysql_pass = "pass";//Corresponding password
$conn = mysql_connect($mysql_server,$mysql_user,$mysql_pass);//MySQL connection string
//If your 5.x and 6.x installations reside on different servers, you will need two sets of server names, usernames, and passwords.
?>
{% endhighlight %}

Also, in my scripts, tables prefixed “drupal.” and “timesnow.” belong to the 5.x and 6.x sites, respectively. Please change these accordingly for your databases.

Lastly, these scripts are not optimized with regard to execution. So do not expect unified inserts to insert multiple tuples.

**User migration script:**

This script, currently, only migrates users, e-mail addresses, uids, and passwords. It does not take care of the profile fields, which can be handled through other customized scripts as profile fields for each installation can differ. It does not import “usernode” content type either. This is coz’ usernodes are typically not required in 6.x, as user profile pages can be styled using tpl.php files themselves. Also, it is important that the uids from your orignal installtion be migrated to your current installation accurately coz' comments and nodes that we will migrate later on are linked to the users through these uids.

There are absolutely no differences between the Users table in 5.x and 6.x. Both of them have the same 18 fields. So you can go ahead and use the following script without even thinking:

{% highlight php %}
< ?php
//first, we delete all users, if any, from the new table, except for the admin
$query = "delete from timesnow.users where uid not in (0,1)";
mysql_query($query);
print "User table cleared.
";
//now we start inserting users into the new table
$query = "select * from drupal.users where uid not in (0,1)";
$queryresult = mysql_query($query);
while ($row = mysql_fetch_row($queryresult)) {
	$query = "insert into timesnow.users values('" . $row[0] . "','" . $row[1] . "','" . $row[2] . "','" . $row[3] . "','" . $row[4] . "','" . $row[5] . "','" . $row[6] . "','" . $row[7] . "','" . mysql_real_escape_string($row[8]) . "','" . $row[9] . "','" . $row[10] . "','" . $row[11] . "','" . $row[12] . "','" . $row[13] . "','" . $row[14] . "','" . $row[15] . "','" . $row[16] . "','" . $row[17] . "')";
	if (!mysql_query($query)) {
		print $query;
	}
	$ucnt += 1;
}
print $ucnt . " users inserted.";
?>
{% endhighlight %}

An easy one that was, wasn’t it. Now, for some taxonomy.

_[Note: You can even take a backup of the old Users table using mysqldump and restore it into the new one. Just make sure that you delete the insertion statements for UIDs 0 and 1.]_

**Migrating taxonomy:**

Now, this is the tricky part. Broken down, the minimal unit of taxonomy data in Drupal is a “term,” which has an associated “tid” (term id). Terms can be fixed taxonomies or free tags. Each node (if applicable) and forum post has an associated tid, which maps it to the term that the user has assigned to it. Therefore, it is imperative that this data is migrated accurately.

The migration script for taxonomy will migrate the hierarchies, term-node relations, vocabularies, etc.:

{% highlight php %}
< ?php
$query = "truncate timesnow.vocabulary";
mysql_query($query);
print "Vocabulary table cleared.
";
$query = "truncate timesnow.vocabulary_node_types";
mysql_query($query);
print "Vocabulary_node_types table cleared. 

";
$query = "truncate timesnow.term_data";
mysql_query($query);
print "Term_data table cleared.
";
$query = "truncate timesnow.term_hierarchy";
mysql_query($query);
print "Term_hierarchy table cleared.
";
$query = "truncate timesnow.term_node";
mysql_query($query);
print "Term_node table cleared.
";
$query = "truncate timesnow.term_relation";
mysql_query($query);
print "Term_relation table cleared.
";
$query = "truncate timesnow.term_synonym";
mysql_query($query);
print "Term_synonym table cleared.
";
print "
";

$query = "select * from drupal.vocabulary";
$queryresult = mysql_query($query);
while ($row = mysql_fetch_row($queryresult)) {
	$query = "insert into timesnow.vocabulary values('" . $row[0] . "','" . mysql_real_escape_string($row[1]) . "','" . mysql_real_escape_string($row[2]) . "','" . mysql_real_escape_string($row[3]) . "','" . $row[4] . "','" . $row[5] . "','" . $row[6] . "','" . $row[7] . "','" . $row[8] . "','" . $row[9] . "','" . $row[10] . "')";

	if (!mysql_query($query)) {
			print $query;
	}
}
print "Vocabulary plugged in.
";

$query = "select * from drupal.vocabulary_node_types";
$queryresult = mysql_query($query);
while ($row = mysql_fetch_row($queryresult)) {
	$query = "insert into timesnow.vocabulary_node_types values('" . $row[0] . "','" . $row[1] . "')";

	if (!mysql_query($query)) {
			print $query;
	}
}
print "Vocabulary node types set.

";

$query = "select * from drupal.term_data";
$queryresult = mysql_query($query);
while ($row = mysql_fetch_row($queryresult)) {
	$query = "insert into timesnow.term_data values('" . $row[0] . "','" . $row[1] . "','" . mysql_real_escape_string($row[2]) . "','" . mysql_real_escape_string($row[3]) . "','" . $row[4] . "')";

	if (!mysql_query($query)) {
			print $query;
	}
}
print "Term_data plugged in.
";

$query = "select * from drupal.term_hierarchy";
$queryresult = mysql_query($query);
while ($row = mysql_fetch_row($queryresult)) {
	$query = "insert into timesnow.term_hierarchy values('" . $row[0] . "','" . $row[1] . "')";

	if (!mysql_query($query)) {
			print $query;
	}
}
print "Term_hierarchy plugged in.
";

$query = "select * from drupal.term_node";
$queryresult = mysql_query($query);
while ($row = mysql_fetch_row($queryresult)) {
	$nid = $row[0];
	$tid = $row[1];
	$query = "select vid from drupal.node where nid = " . $nid;
	$qr2 = mysql_query($query);
	$row2 = mysql_fetch_row($qr2);
	$vid = $row2[0];
	$query = "insert into timesnow.term_node values('" . $nid . "','" . $vid . "','" . $tid . "')";

	if (!mysql_query($query)) {
			print $query;
	} else {
		$tncnt += 1;
	}
}
print "Term_node plugged in (" . $tncnt . " records entered).
";
?>
{% endhighlight %}

You must’ve noticed that I am clearing out all associated tables before each insert block. This is coz’ it is important that the tids, tid-vid pairings, and tid-nid pairings be unique and as they are in the old database. Otherwise it will cause either of the following two problems: (a) terms won’t get attached to your nodes accurately or (b) the insert statements will throw primary key and/or unique key errors.
This script is out-of-the-box too. With just the database name changes (from timesnow. to your database name), it can be run as it is.

And now for the big Kahuna—migrating forum posts.

**Migrating forums:**

Three parts to this script—forum topics have to be attached to their respective terms, forums posts have to go into the node table, and forum comments have to be migrated. The first part has already been taken care of in the previous script, so we are simply dealing with inserting comments and forum nodes. Again an out-of-box script, this one:

{% highlight php %}
< ?php
$cnt = 0;
$commentcnt = 0;
$query = "select * from drupal.node where type = 'forum'";
$noders = mysql_query($query);
while ($row = mysql_fetch_row($noders)) {
	$nid = $row[0];
	$vid = $row[1];
	$type = $row[2];
	$title = mysql_real_escape_string($row[3]);
	$uid = $row[4];
	$status = $row [5];
	$created = $row[6];
	$changed = $row[7];
	$comment = $row[8];
	$promote = $row[9];
	$moderate = $row[10];
	$sticky = $row[11];
	//Insertion into node
	$query="insert into timesnow.node values('" . $nid . "','" . $vid . "','" . $type . "','','" . $title . "','" . $uid . "','" . $status . "','" . $created . "','" . $changed . "','" . $comment . "','" . $promote . "','" . $moderate . "','" . $sticky . "','0','0')";
	if (!mysql_query($query)) {
		print $query;
	}

	$query = "select * from drupal.node_revisions where nid = " . $nid;
	$node_revisionrs = mysql_query($query);
	$nrow = mysql_fetch_row($node_revisionrs);
	$body = mysql_real_escape_string($nrow[4]);
	$teaser = mysql_real_escape_string($nrow[5]);
	$log = mysql_real_escape_string($nrow[6]);
	$timestamp = $nrow[7];
	$format = mysql_real_escape_string($nrow[8]);
	//Insertion into node_revision
	$query="insert into timesnow.node_revisions values('" . $nid . "','" . $vid . "','" . $uid . "','" . $title . "','" . $body . "','" . $teaser . "','" . $log . "','" . $timestamp . "','1')";
	if (!mysql_query($query)) {
		print $query;
	}

	$query = "select * from drupal.node_comment_statistics where nid = " . $nid;
	$node_comment_statistics_rs = mysql_query($query);
	$ncsrow = mysql_fetch_row($node_comment_statistics_rs);
	$last_comment_timestamp = $ncsrow[1];
	$last_comment_name = mysql_real_escape_string($ncsrow[2]);
	$last_comment_uid = $ncsrow[3];
	$comment_count = $ncsrow[4];
	//Insertion into node_comment_statisitcs
	$query = "insert into timesnow.node_comment_statistics values ('" . $nid . "','" . $last_comment_timestamp . "','" . $last_comment_name. "','" . $last_comment_uid . "','" . $comment_count. "')";
	if (!mysql_query($query)) {
		print $query;
	}

	$query = "select * from drupal.comments where nid = " . $nid;
	$commentrs = mysql_query($query);
	while ($row = mysql_fetch_row($commentrs)) {
		$query = "insert into timesnow.comments values ('" . $row[0] . "','" . $row[1] . "','" . $row[2] . "','" . $row[3] . "','" . mysql_real_escape_string($row[4]) . "','" . mysql_real_escape_string($row[5]) . "','" . $row[6] . "','" . $row[7] . "','" . $row[9] . "','" . $row[10] . "','" . $row[11] . "','" . $row[13] . "','" . $row[14] . "','" . $row[15] . "')";
		if (!mysql_query($query)) {
			print $query;
		}

		$commentcnt += 1;
	}
	$cnt += 1;
}

print $cnt . " rows inserted.
";
print $commentcnt . " comments inserted.
";

//Inserting into forum
$query = "select * from drupal.forum";
$queryresult = mysql_query($query);
while ($row = mysql_fetch_row($queryresult)) {
	$nid = $row[0];
	$vid = $row[1];
	$tid = $row[2];
	$query = "insert into timesnow.forum values('" . $nid . "','" . $vid . "','" . $tid . "')";

	if (!mysql_query($query)) {
			print $query;
	} else {
		$forumcnt += 1;
	}
}
print $forumcnt . " records updated into the forum table.
";
?>
{% endhighlight %}

Certain table structures have changed in 6.x. But these scripts handle those too.

Now, moving on into content migration. Due to CCK, content migration will have to take into account many different things. I am not going to post a generic content migration script, coz’ I don’t have one, and that is coz’, frankly, I did not need one. I just needed one for a simple content type with just a title and body and terms of all sorts associated with it, with comments.

My content type was called “multi_user_blog.” You may have to change that for your needs. But basically, what the following script does is inserts content title, body, and associates comments. It is like a building block for all your node migrations. A lot of things have changed in Drupal 6.x when it comes to nodes and associated comments and revisions. There have been translation fields added to the node table itself. Also, the vid (version id) in the node_revision table is set to auto increment in 6.x. This vid is also associated with different terms. Thus, each version can now have its own terms in 6.x.

Moving right along to the script, it is pretty straight forward:

{% highlight php %}
< ?php
$query = "truncate timesnow.node";
mysql_query($query);
print "Node table cleared.
";
$query = "truncate timesnow.node_revisions";
mysql_query($query);
print "Node_revisions table cleared.
";
$query = "truncate timesnow.node_comment_statistics";
mysql_query($query);
print "Node_comment_statistics table cleared.
";
$query = "truncate timesnow.comments";
mysql_query($query);
print "Comments table cleared.
";
$query = "truncate timesnow.forum";
mysql_query($query);
print "Forum table cleared.
";

?>

# Inserting multi_user_blogs
< ?php
$query = "select * from drupal.node where type = 'multi_user_blog'";
$noders = mysql_query($query);
while ($row = mysql_fetch_row($noders)) {
	$nid = $row[0];
	$vid = $row[1];
	$type = $row[2];
	$title = mysql_real_escape_string($row[3]);
	$uid = $row[4];
	$status = $row [5];
	$created = $row[6];
	$changed = $row[7];
	$comment = $row[8];
	$promote = $row[9];
	$moderate = $row[10];
	$sticky = $row[11];
	//Insertion into node
	$query="insert into timesnow.node values('" . $nid . "','" . $vid . "','" . $type . "','','" . $title . "','" . $uid . "','" . $status . "','" . $created . "','" . $changed . "','" . $comment . "','" . $promote . "','" . $moderate . "','" . $sticky . "','0','0')";
	if (!mysql_query($query)) {
		print $query;
	}

	$query = "select * from drupal.node_revisions where nid = " . $nid;
	$node_revisionrs = mysql_query($query);
	$nrow = mysql_fetch_row($node_revisionrs);
	$body = mysql_real_escape_string($nrow[4]);
	$teaser = mysql_real_escape_string($nrow[5]);
	$log = mysql_real_escape_string($nrow[6]);
	$timestamp = $nrow[7];
	$format = mysql_real_escape_string($nrow[8]);
	//Insertion into node_revision
	$query="insert into timesnow.node_revisions values('" . $nid . "','" . $vid . "','" . $uid . "','" . $title . "','" . $body . "','" . $teaser . "','" . $log . "','" . $timestamp . "','" . $format . "')";
	if (!mysql_query($query)) {
		print $query;
	}

	$query = "select * from drupal.node_comment_statistics where nid = " . $nid;
	$node_comment_statistics_rs = mysql_query($query);
	$ncsrow = mysql_fetch_row($node_comment_statistics_rs);
	$last_comment_timestamp = $ncsrow[1];
	$last_comment_name = mysql_real_escape_string($ncsrow[2]);
	$last_comment_uid = $ncsrow[3];
	$comment_count = $ncsrow[4];
	//Insertion into node_comment_statisitcs
	$query = "insert into timesnow.node_comment_statistics values ('" . $nid . "','" . $last_comment_timestamp . "','" . $last_comment_name. "','" . $last_comment_uid . "','" . $comment_count. "')";
	if (!mysql_query($query)) {
		print $query;
	}

	$query = "select * from drupal.comments where nid = " . $nid;
	$commentrs = mysql_query($query);
	while ($row = mysql_fetch_row($commentrs)) {
		$query = "insert into timesnow.comments values ('" . $row[0] . "','" . $row[1] . "','" . $row[2] . "','" . $row[3] . "','" . mysql_real_escape_string($row[4]) . "','" . mysql_real_escape_string($row[5]) . "','" . $row[6] . "','" . $row[7] . "','" . $row[9] . "','" . $row[10] . "','" . $row[11] . "','" . $row[13] . "','" . $row[14] . "','" . $row[15] . "')";
		if (!mysql_query($query)) {
			print $query;
		}

		$commentcnt += 1;
	}

	$cnt += 1;
}
print $cnt . " rows inserted.
";
print $commentcnt . " comments inserted.
";
?>
{% endhighlight %}

Studying this script can you give you some clues/insights to migrating content on your own site.
I am working on an auto content migration script that will enable migration of all content types, associated tables, fields, etc.

Also in works is the profile migration script.

In the meanwhile, let me know if I can improve on these OR if you have some script to contribute (I will credit, promise). :-)

If you do crack the auto content migration script for all CCK content types before me I will take you out for a meal, and throw in a free conversation, if you are in a can-buy-me-a-meal range. If you feel like my efforts deserve a meal, I am a complete meal slut. Oh, and why meals? Coz’ they are fun and desirable and leave one satisfied and contented.

_That’s all folks!_