---
layout: post
title: Locales in Ubuntu
modified:
categories: blog
excerpt:
tags: []
image:
  feature:
---
If you ever had to create an internationalized application on a LAMP stack, you are probably familiar with locales in your Linux distro. For the purpose of this discussion, we will assume the Linux distro to be Ubuntu Server 10.04 LTS. Each locale that you install contains a lot of information such as currency representation, date/time adjustments, decimal point representation, and LTR/RTL text. You can read more about locales here: https://help.ubuntu.com/community/Locale.

What I am going to talk about is how to use these locales in your LAMP-based finance applications. We all know that currency is represented differently everywhere. For example, an amount of 1235.56 would be represented as:

<ul>
<li>¥1236 (Japanese Yen: Notice that there is no space between the currency symbol and the figure; also, the decimal points have disappeared)</li>
<li>$1,235.56 (US Dollar)</li>
<li>1.235,56 € (German Euro: Notice that the thousand separator is a period and the decimal separator is a comma; also, the euro currency symbol appears after the figure)</li>
</ul>

If a single application is to handle multiple currency representations, it can be handled very easily in PHP using money_format and the setlocale (http://php.net/manual/en/function.setlocale.php) function. For example,

<php>
$amount = 1234.56;
$locale = "nl_NL"; //Could be the locale you want; for example, zh_TW, ja_JP, en_US, etc.
setlocale(LC_MONETARY,$locale);
print money_format("%n",$amount); //More information can be found at http://in2.php.net/manual/en/function.money-format.php
</php>

It is a very powerful feature and saves developers days in coding time.

To see what locales have already been installed on your system/server, you can use the following command:

<bash>
locale -a
</bash>

To install additional locales:
<bash>
sudo apt-get install language-pack-de-base //German
sudo apt-get install language-pack-es-base //Spanish
sudo apt-get install language-pack-ja-base //Japanese
sudo apt-get install language-pack-ko-base //Korean
sudo apt-get install language-pack-zh-base //Chinese
sudo apt-get install language-pack-pt-base //Portugese
//There are tons more available.
</bash>

Once installed, make sure you reconfigure your locales using the following two commands:

<bash>
sudo dpkg-reconfigure locales
sudo locale-gen
</bash>

And since this is all open source, you can do other crazy shit too. For example, the ISO specifications for Taiwan say that there is a decimal point in Taiwanese currency. However, in reality, that decimal point is so low that transaction there do not consider the decimal point at all. So no one will say that an apple costs NT$ 12.23&mdash;it is is NT$12. If you are developing an application for Taiwan and still wish to use locales, you can simply modify your locale file for Taiwan to instruct it to not use locales. The file will be available at "/usr/share/i18n/locales/zh_TW." In this file, you can modify the following parameters:

<bash>
int_frac_digits         0
frac_digits             0
</bash>

And voila, we are done! 

You can mess with some other options in those locale files too and, if you are feeling really adventorous, create your own :-)