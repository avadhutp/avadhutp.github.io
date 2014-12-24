---
layout: post
title: Visual sick
modified:
categories: blog
excerpt:
tags: []
image:
  feature:
---
<a href="http://sentimentalminions.files.wordpress.com/2007/06/suggestion_list.png" title="suggestion_list.png"></a>Finally, I have uploaded the screen-shots of my Intranet. A wonderfully powerful and ludicrously flexible system implement in <a target="_blank" href="http://www.drupal.org">Drupal</a>. I wish I could upload a working version somewhere, but my company policies would not allow me to do so.

<a href="http://sentimentalminions.files.wordpress.com/2007/06/front1.png" title="front1.png"><img align="baseline" src="http://sentimentalminions.files.wordpress.com/2007/06/front1.thumbnail.png" alt="front1.png" /></a>

<u>Frontpage</u>: Custom handcrafted theme, high-clarity PNG icons, etc. The layout and look of the page was possible due to two Drupal modules—Views and Panels. The event calendar on the left is AJAX based and also generates iCal feeds (for MS Outlook). RSS 2.0 feeds are listed at the bottom.  

<a href="http://sentimentalminions.files.wordpress.com/2007/06/handbook.png" title="handbook.png"><img align="bottom" src="http://sentimentalminions.files.wordpress.com/2007/06/handbook.thumbnail.png" alt="handbook.png" /></a>

<u>Handbook</u>: Uses the book module. Again modded and themed to suit our requirements.

<a href="http://sentimentalminions.files.wordpress.com/2007/06/submit-post.png" title="submit-post.png"><img align="absBottom" src="http://sentimentalminions.files.wordpress.com/2007/06/submit-post.thumbnail.png" alt="submit-post.png" /></a>

<u>Posts</u>: Being editors, my colleagues wanted everything from the posting section's WYSIWYG editor. The answer was the FCKEditor. I modded it a bit to better suit our requirements. For post classification, I used a hybrid categorization technique wherein primary post classification is done by selecting hard-coded categories and secondary classification is performed by specifying (optional) free tags. Primary classification determines where all the posts show up, and secondary, aids discoverability.

<a href="http://sentimentalminions.files.wordpress.com/2007/06/forums.png" title="forums.png"><img align="bottom" src="http://sentimentalminions.files.wordpress.com/2007/06/forums.thumbnail.png" alt="forums.png" /></a>

<u>Forums</u>: Taxonomy module facilitated the creation of several nested/flat forums. They are a big hit in the office, accounting for more than 35% of all the interaction. Discussions regarding everthing—from new process reengineering excercises to Intranet look &amp; feel—were discussed on forums.

<a href="http://sentimentalminions.files.wordpress.com/2007/06/post_single.png" title="post_single.png"><img align="baseline" src="http://sentimentalminions.files.wordpress.com/2007/06/post_single.thumbnail.png" alt="post_single.png" /></a>

<u>A simple node page</u>: Shows how a single post/article looks. Notice the green arrows indicating nested comments—makes it easier to track conversations.

<a href="http://sentimentalminions.files.wordpress.com/2007/06/posts.png" title="posts.png"><img align="baseline" src="http://sentimentalminions.files.wordpress.com/2007/06/posts.thumbnail.png" alt="posts.png" /></a>

<u>Post tracker</u>: Tracks all recent posts, new comments, etc. Life has been easy thanks to the Views module.

<a href="http://sentimentalminions.files.wordpress.com/2007/06/library.png" title="library.png"><img align="baseline" src="http://sentimentalminions.files.wordpress.com/2007/06/library.thumbnail.png" alt="library.png" /></a>

<u>Library</u>: Custom node type, Views, &amp; jRating.

<a href="http://sentimentalminions.files.wordpress.com/2007/06/library_inner.png" title="library_inner.png"><img align="baseline" src="http://sentimentalminions.files.wordpress.com/2007/06/library_inner.thumbnail.png" alt="library_inner.png" /></a>

<u>Library listing</u>: A book listing from the library. Custom data type themed using the ConTemplate module. Notice the AJAX-based rating module at the bottom. The "loan now" link auto-generates an email in MS Outlook and sends it to the designated librarian. Books loaned out do not have the loan now link, instead they display information regarding who has loaned the book, when, and when he/she plans to return it!

<a href="http://sentimentalminions.files.wordpress.com/2007/06/recommend.png" title="recommend.png"><img align="baseline" src="http://sentimentalminions.files.wordpress.com/2007/06/recommend.thumbnail.png" alt="recommend.png" /></a>

<u>Recommend a resource</u>: Allows anyone to recommend books/subscriptions for the organization to buy/subscribe to. Generated using the WebForm module. It auto mails the forms info to the designated person. Also, it allows someone with enough privelegs to view statistics regarding form submissions and download all submissions in a EXCEL format!

<a href="http://sentimentalminions.files.wordpress.com/2007/06/microsite.png" title="microsite.png"><img align="baseline" src="http://sentimentalminions.files.wordpress.com/2007/06/microsite.thumbnail.png" alt="microsite.png" /></a>

<u>Microsite</u>: A microsite developed entirely within the new system. Notice how the left and right blocks have changed with the change in site.

<a href="http://sentimentalminions.files.wordpress.com/2007/06/calendar.png" title="calendar.png"><img align="bottom" src="http://sentimentalminions.files.wordpress.com/2007/06/calendar.thumbnail.png" alt="calendar.png" /></a>

<u>Event calendar</u>: Allows all to view role-based events, download iCal feed for one/all events, add comments to events, etc. This page view feeds directly into/from the small block on the right.

And finally, for some shock value, below the graphical representation of the database schema—<a href="http://sentimentalminions.files.wordpress.com/2007/06/db_schema.png" title="db_schema.png">db_schema.png</a>—knock yourself out!