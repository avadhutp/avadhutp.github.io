---
layout: post
title: Setting up WKTHMLTOPDF on Ubuntu Server 10.04
modified:
categories: blog
excerpt:
tags: []
image:
  feature:
---
Generating PDFs is, I feel, is the most challenging part of developing any web application. In our particular case, this was made even more difficult considering the challenges that lay ahead of us:

* **Several languages**: Our PDF generation engine would need to support Chinese, Japanese, Korean, German, Portugese, and even some RTL languages.
* **Layouts**: Our invoice layouts were created in HTML and were quite crazy. Some even had styling performed by post-load JS. ::gulp::
* **Tables**: They should break gracefully with page-breaks.
* **Complex headers/footers**: Images, page numbers, etc.
* **Fast**: Goes without saying, right?

In the past, we had experimented with several engines such as [PrinceXML](http://code.google.com/p/dompdf/), but it was tad bit expensive.

One of my colleagues at work, Bharat Mhaskar, evaluated WKHTMLTOPDF and realized that with the QT patch, it would be able to render accurate PDFs given semantically correct HTML and JS. He spent days on perfecting the method to install it on Ubuntu 10.04 server with the QT patch and it is something that we've been using extensively in our apps. Below, I am outlining the installation procedure, possibly saving several people out there copious amounts of time:

1. Install  xvfb to run wkhtmltopdf headless:
{% highlight bash %}
sudo apt-get  install xvfb
{% endhighlight %}

2. Install  libraries that are required:
{% highlight bash %}
sudo aptitude install openssl build-essential xorg libssl-dev libxrender-dev
{% endhighlight %}
{% highlight bash %}
sudo apt-get build-dep qt4-x11
{% endhighlight %}

3. Install required fonts:
{% highlight bash %}
sudo apt-get install msttcorefonts
{% endhighlight %}
{% highlight bash %}
sudo apt-get install ttf-ipafont-*
{% endhighlight %}
If this gives error then make sure that universe and multiverse is enabled in your apt-get repository.

4. Install  git source of wkhtmltopdf:
{% highlight bash %}
sudo aptitude install git-core
{% endhighlight %}
{% highlight bash %}
git clone git://github.com/antialize/wkhtmltopdf.git wkhtmltopdf
{% endhighlight %}
{% highlight bash %}
git clone git://gitorious.org/+wkhtml2pdf/qt/wkhtmltopdf-qt.git wkhtmltopdf-qt
{% endhighlight %}
{% highlight bash %}
cd wkhtmltopdf-qt
{% endhighlight %}
{% highlight bash %}
git checkout staging
{% endhighlight %}
{% highlight bash %}
cat ../wkhtmltopdf/static_qt_conf_base ../wkhtmltopdf/static_qt_conf_linux | sed -re 's/#.*//'
{% endhighlight %}
{% highlight bash %}
cd wkhtmltopdf-qt
{% endhighlight %}
{% highlight bash %}
./configure -nomake tools,examples,demos,docs,translations -opensource -prefix "../wkqt"
{% endhighlight %}{% highlight bash %}
make -j3 && make install
{% endhighlight %}
{% highlight bash %}
cd ..
{% endhighlight %}
{% highlight bash %}
cd wkhtmltopdf
{% endhighlight %}
{% highlight bash %}
../wkqt/bin/qmake
{% endhighlight %}
{% highlight bash %}
make && make install
{% endhighlight %}

5. Install [PDFtk](http://www.pdflabs.com/tools/pdftk-the-pdf-toolkit/). This is an optional step; the library helps stitch together several PDFs.
{% highlight bash %}
sudo apt-get install pdftk
{% endhighlight %}

6. Install actual wkpdftohtml:
{% highlight bash %}
wget http://wkhtmltopdf.googlecode.com/files/wkhtmltopdf-0.9.9-static-amd64.tar.bz2 
{% endhighlight %}
{% highlight bash %}
tar xvjf wkhtmltopdf-0.9.9-static-amd64.tar.bz2
{% endhighlight %}
{% highlight bash %}
mv wkhtmltopdf-amd64 /usr/local/bin/wkhtmltopdf
{% endhighlight %}
{% highlight bash %}
chmod +x /usr/local/bin/wkhtmltopdf
{% endhighlight %}
Reference: [https://github.com/jdpace/PDFKit/wiki/Installing-WKHTMLTOPDF](https://github.com/jdpace/PDFKit/wiki/Installing-WKHTMLTOPDF)

7. Install whatever fonts you need. If you have an Ubuntu desktop machine, it is even simpler. Install locales using the GUI on your desktop machine. This will also install the required fonts. Then, copy these to the target machine:
{% highlight bash %}
scp /usr/share/fonts/ user@PDFGenerator_TARGET_MACHINE/usr/share/fonts
{% endhighlight %}

8. Rebuild font cache:
{% highlight bash %}
fc-cache -fv
{% endhighlight %}

9. Test wkhtmltopdf:
{% highlight bash %}
xvfb-run -a -s "-screen 0 640x480x16" wkhtmltopdf --dpi 200  --page-size A4 --margin-top 150  http://www.editage.kr korea.pdf
{% endhighlight %}

Seems like a longish process but it works wonders. For a comprehensive list of features/options that are possible, see the [WKTMLTOPDF manual](http://madalgo.au.dk/~jakobt/wkhtmltoxdoc/wkhtmltopdf-0.9.9-doc.html).

We deployed our PDF generation machine onto an Amazon EC2 instance and developed an API so that all our apps can use the same PDF generation engine. We are also rolling out our own Drupal module that wraps this functionality and can be used by most site builders as a drop-in—all current PDF conversion module for Drupal require than an additional library be installed. This, hopefully, will allow Drupallers to roll out PDF conversion capabilities on their sites with minimal effort. :-)