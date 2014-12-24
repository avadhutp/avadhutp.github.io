---
layout: post
title: Line breaks in textarea default value in Drupal 7
modified:
categories: blog
excerpt:
tags: []
image:
  feature:
date: 2014-08-08T15:39:55-04:00
---
<p>Form API is magnificent. I love the idea of having to write as little markup as possible and having the framework render the most of the gritty HTML. This ensures that developers come and go but the high standards the Drupal community has set for it's code, are upheld.</p>
<p>Moving along, today's problem is as irritating as it is simple. I had to insert some default text into a textarea field in Drupal 7. And obviously I was using Form API. However, the tricky part was that I had to insert line breaks in the default text.</p>
<p>Typically, this is very simple to do in pure HTML/PHP. You simply incorporate the line breaks into your markup itself:</p>
[html]
<TEXTAREA name="trickyText">
This is the first line.
And here comes the second one.
</TEXTAREA>
[/html]
<p>In Drupal though, you have to make do with the <code>#default_value</code> parameter. Now you'd think that a <code>\n</code> or <code>\n\r</code> would suffice. But those two just refuse to do anything&mdash;Drupal's Form API spits them out as is. What you need is <code>&#13;&#10;</code>. And while we are at it, a <code>&#09;</code> would produce a tab.</p>
<p>Here's what my code looked like:</p>
<php>
function formGeneratorFunction($form_state){
 //This bit is just to impore the readability of the code
 $defVal[] = '<html>';
 $defVal[] = '&#09;<body>';
 $defVal[] = '&#09;&#09;Hello world!';
 $defVal[] = '&#09;</body>';
 $defVal[] = '</html>';

 $conHtmlForm['rawHtml'] = array(
  '#title' => t('Raw HTML code'),
  '#type' => 'textarea',
  '#description' => t('Enter valid HTML. You can include inline CSS, JS, and even remote images.'),
  '#required' => true,
  '#default_value' => html_entity_decode(implode('&#13;&#10;', $defVal))
 );
 $conHtmlForm['submit'] = array(
  '#type' => 'submit',
  '#value' => t('Submit')
 );

 return $conHtmlForm;
}
</php>
<p>And it produced results exactly the way I wanted it to:
<img src="http://img7.imagebanana.com/img/d928p0ns/Selection_002.png" />
</p>