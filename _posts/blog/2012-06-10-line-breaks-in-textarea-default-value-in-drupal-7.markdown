---
layout: post
title: Line breaks in textarea default value in Drupal 7
modified:
categories: blog
excerpt:
tags: []
image:
  feature:
---
Form API is magnificent. I love the idea of having to write as little markup as possible and having the framework render the most of the gritty HTML. This ensures that developers come and go but the high standards the Drupal community has set for it's code, are upheld.

Moving along, today's problem is as irritating as it is simple. I had to insert some default text into a textarea field in Drupal 7. And obviously I was using Form API. However, the tricky part was that I had to insert line breaks in the default text.

Typically, this is very simple to do in pure HTML/PHP. You simply incorporate the line breaks into your markup itself:
{% highlight html %}
<TEXTAREA name="trickyText">
This is the first line.
And here comes the second one.
</TEXTAREA>
{% endhighlight %}

In Drupal though, you have to make do with the `#default_value` parameter. Now you'd think that a `\n` or `\n\r` would suffice. But those two just refuse to do anything—Drupal's Form API spits them out as is. What you need is `&#13;&#10;`. And while we are at it, a `&#09;` would produce a tab.

Here's what my code looked like:

{% highlight php %}
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
{% endhighlight %}

And it produced results exactly the way I wanted it to:
![img](http://img7.imagebanana.com/img/d928p0ns/Selection_002.png)
