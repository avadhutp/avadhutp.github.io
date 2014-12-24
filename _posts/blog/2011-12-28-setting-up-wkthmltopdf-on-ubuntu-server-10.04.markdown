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
<p>Generating PDFs is, I feel, is the most challenging part of developing any web application. In our particular case, this was made even more difficult considering the challenges that lay ahead of us:</p>
<ul>
	<li><strong>Several languages</strong>: Our PDF generation engine would need to support Chinese, Japanese, Korean, German, Portugese, and even some RTL languages.</li>
	<li><strong>Layouts</strong>: Our invoice layouts were created in HTML and were quite crazy. Some even had styling performed by post-load JS. ::gulp::</li>
	<li><strong>Tables</strong>: They should break gracefully with page-breaks.</li>
	<li><strong>Complex headers/footers</strong>: Images, page numbers, etc.</li>
	<li><strong>Fast</strong>: Goes without saying, right?</li>
</ul>
<p>In the past, we had experimented with several engines such as <a href="http://code.google.com/p/dompdf/" target="_blank">DOMPDF</a> and <a href="http://www.xhtml2pdf.com/" target="_blank">XHTML2PDF</a>. However, they failed on one count or the other when it came to the challenges mentioned above. The only engine that I evaluated that fulfilled all of the above criteria was <a href="http://www.princexml.com/purchase/" target="_blank">PrinceXML</a>, but it was tad bit expensive.</p>
<p>One of my colleagues at work, Bharat Mhaskar, evaluated WKHTMLTOPDF and realized that with the QT patch, it would be able to render accurate PDFs given semantically correct HTML and JS. He spent days on perfecting the method to install it on Ubuntu 10.04 server with the QT patch and it is something that we've been using extensively in our apps. Below, I am outlining the installation procedure, possibly saving several people out there copious amounts of time:</p>
<p>1. Install  xvfb to run wkhtmltopdf headless:
<bash>
sudo apt-get  install xvfb
</bash>
</p>
<p>2. Install  libraries that are required:
<bash>
sudo aptitude install openssl build-essential xorg libssl-dev libxrender-dev
</bash>
<bash>
sudo apt-get build-dep qt4-x11
</bash>
</p>
<p>3. Install required fonts:
<bash>
sudo apt-get install msttcorefonts
</bash>
<bash>
sudo apt-get install ttf-ipafont-*
</bash>
If this gives error then make sure that universe and multiverse is enabled in your apt-get repository.
</p>
<p>4. Install  git source of wkhtmltopdf:
<bash>
sudo aptitude install git-core
</bash>
<bash>
git clone git://github.com/antialize/wkhtmltopdf.git wkhtmltopdf
</bash>
<bash>
git clone git://gitorious.org/+wkhtml2pdf/qt/wkhtmltopdf-qt.git wkhtmltopdf-qt
</bash>
<bash>
cd wkhtmltopdf-qt
</bash>
<bash>
git checkout staging
</bash>
<bash>
cat ../wkhtmltopdf/static_qt_conf_base ../wkhtmltopdf/static_qt_conf_linux | sed -re 's/#.*//'
</bash>
<bash>
cd wkhtmltopdf-qt
</bash>
<bash>
./configure -nomake tools,examples,demos,docs,translations -opensource -prefix "../wkqt"
</bash><bash>
make -j3 && make install
</bash>
<bash>
cd ..
</bash>
<bash>
cd wkhtmltopdf
</bash>
<bash>
../wkqt/bin/qmake
</bash>
<bash>
make && make install
</bash>
</p>
<p>5. Install <a href="http://www.pdflabs.com/tools/pdftk-the-pdf-toolkit/" target="_blank">PDFtk</a>. This is an optional step; the library helps stitch together several PDFs.
<bash>
sudo apt-get install pdftk
</bash>
</p>
<p>6. Install actual wkpdftohtml:
<bash>
wget http://wkhtmltopdf.googlecode.com/files/wkhtmltopdf-0.9.9-static-amd64.tar.bz2 
</bash>
<bash>
tar xvjf wkhtmltopdf-0.9.9-static-amd64.tar.bz2
</bash>
<bash>
mv wkhtmltopdf-amd64 /usr/local/bin/wkhtmltopdf
</bash>
<bash>
chmod +x /usr/local/bin/wkhtmltopdf
</bash>
Reference: <a href="https://github.com/jdpace/PDFKit/wiki/Installing-WKHTMLTOPDF" target="_blank">https://github.com/jdpace/PDFKit/wiki/Installing-WKHTMLTOPDF</a>
</p>
<p>7. Install whatever fonts you need. If you have an Ubuntu desktop machine, it is even simpler. Install locales using the GUI on your desktop machine. This will also install the required fonts. Then, copy these to the target machine:
<bash>
scp /usr/share/fonts/ user@PDFGenerator_TARGET_MACHINE/usr/share/fonts
</bash>
</p>
<p>8. Rebuild font cache:
<bash>
fc-cache -fv
</bash>
</p>
<p>9. Test wkhtmltopdf:
<bash>
xvfb-run -a -s "-screen 0 640x480x16" wkhtmltopdf --dpi 200  --page-size A4 --margin-top 150  http://www.editage.kr korea.pdf
</bash>
</p>
<p>Seems like a longish process but it works wonders. For a comprehensive list of features/options that are possible, see the <a href="http://madalgo.au.dk/~jakobt/wkhtmltoxdoc/wkhtmltopdf-0.9.9-doc.html" target="_BLANK">WKTMLTOPDF manual</a>.</p>
<p>We deployed our PDF generation machine onto an Amazon EC2 instance and developed an API so that all our apps can use the same PDF generation engine. We are also rolling out our own Drupal module that wraps this functionality and can be used by most site builders as a drop-in&mdash;all current PDF conversion module for Drupal require than an additional library be installed. This, hopefully, will allow Drupallers to roll out PDF conversion capabilities on their sites with minimal effort. :-)</p>