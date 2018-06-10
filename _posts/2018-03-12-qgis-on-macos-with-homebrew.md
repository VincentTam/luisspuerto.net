---
id: 1571
title: QGIS on macOS with Homebrew
date: 2018-03-12T14:31:26+00:00
author: Luis Puerto
layout: 
guid: http://luisspuerto.net/?p=1571
permalink: /2018/03/qgis-on-macos-with-homebrew/
image: /wp-content/uploads/2018/03/QGIS_logo_2017.svg.png
categories:
  - GIS
  - Personal
  - Technology
tags:
  - homebrew
  - macOS
  - qgis
---
**Update Monday, 26th of March**: Almost the next day or the following day I published this post [KyngChaos](http://www.kyngchaos.com) just published the QGIS 3 release for macOS. You can download [here](http://www.kyngchaos.com/software/qgis).

* * *

Just a couple days ago I was about to write a post about how difficult, or almost impossible, is to install the last versions of QGIS on macOS right now. Everything started, I believe, when [gdal](http://www.gdal.org) package got update at the beginning of the week and that broke my install of QGIS 2 even thought, and to be honest, I really don&#8217;t know if all this mess was caused for that update, or by the update of the python formulae in Homebrew. Anyhow, usually these problems are solved just rebuilding the app from source using the new packages, but in this occasion that solution wasn&#8217;t working.

Homebrew decided that on the first of March the behavior of the Python formulae and the name, and I believe the name of the formulae, was going to change to be more consistent. To sum a little bit up, after the change `python` was going to point to `python3` in your shell and `python2` is going point to so `python2` –or something like that. However, the change in the command in the CLI  broke a truckload of third party software, and seems that also wasn&#8217;t compliant with [Python&#8217;s policy](https://www.python.org/dev/peps/pep-0394/). So&#8230; they decided [to change some things back](https://discourse.brew.sh/t/python-and-pep-394/1813) –CLI commands, no formulae names– and now seems that `python` ➜ `python2` and `python3` ➜ `python3`. If you are confused, don&#8217;t worry, am I also. Besides, since I&#8217;m not an expert in the matter and I&#8217;ve perhaps missed something in all this chaos. In consequence&#8230; **[I really don&#8217;t know!](https://media1.tenor.com/images/499592e35e9cdf56bfdcb11eaf9d891d/tenor.gif?itemid=5607416)**.

Anyhow and right now, with the current state of Homebrew and the [OSGeo/homebrew-osgeo4mac](https://github.com/OSGeo/homebrew-osgeo4mac) tap, it&#8217;s quite difficult to install the last versions of QGIS in macOS. A few weeks ago, QGIS released QGIS 3 Girona, however it&#8217;s only possible to install it right now in Windows and Linux. Usually on macOS the releases has been a little bit slower than in other platforms basically because QGIS has really few developers able to work on macOS and seems that things aren&#8217;t as easy as in other platforms. Traditionally, Homebrew has come to the rescue with [OSGeo/homebrew-osgeo4mac](https://github.com/OSGeo/homebrew-osgeo4mac) tap, which you can build your own version of QGIS, but that repo hasn&#8217;t been update almost since the beginning of December. This time the delay not only mean that the macOS users not only can&#8217;t install the last small incremental change version, but that for more than 3 weeks we aren&#8217;t able to install the last version, which means a big leap from version 2 to 3.

This left the macOS users a little bit hanging although we still can download the _official_ build for macOS in the [KyngChaos](http://www.kyngchaos.com/software/qgis){.reference.external} download page, where you can find the 2.18.14 version. The problem is that version isn&#8217;t even the last version of the [long-term repository release](https://en.wikipedia.org/wiki/Long-term_support), which is 2.18.17.

Nevertheless, this weekend I was able to rebuild the last version of the LTR of QGIS 2 and the developer version of QGIS3. I really don&#8217;t know how stable is the QGIS 3 developer version, but at least at first sight everything works and the app even open faster than the QGIS 2.

# How to install the last version of QGIS 2?

What I did was just reinstall `python`, `python2`, `gdal`, `gdal2`, and finally `qgis2`. More or less as follows:

<pre class="lang:sh decode:true " title="Reinstalling everything">$ brew reinstall python2 python bison &&
brew reinstall gdal2 \
    --with-armadillo --with-complete --with-libkml \
    --with-opencl --with-postgresql --with-unsupported
$ brew reinstall qgis2 --with-grass --with-saga-gis-lts</pre>

After that and compiling here and there, I was able to open QGIS.app again.

This left me with QGIS in `/usr/local/Cellar/qgis2/2.18.14`. But I want to have QGIS.app in `/Applications` folder so I have two options. First one is run in the following command in the terminal, as advised by Homebrew:

<pre class="lang:sh decode:true">$ brew linkapps [--local]</pre>

However, I prefer to move the actual app to the `/Applications` folder and create a symbolic link on the `/Cellar` folder.

<pre class="lang:sh decode:true" title="Moving an symbolic linking QGIS 2">$ mv -f /usr/local/Cellar/qgis2/2.18.14/QGIS.app /Applications
$ ln -s /Applications/QGIS.app /usr/local/Cellar/qgis2/2.18.14/QGIS.app</pre>

Yet, weren&#8217;t we going to install the last version of QGIS? This isn&#8217;t the last version. OK&#8230;

## QGIS 2.18.17

To install the last version you need to change the the QGIS formulae to make it to download the last version from the LTR. Since Homebrew isn&#8217;t anything more than a git with Ruby scripts I recommend you to make a branch in the repository and change the formula. You aren&#8217;t going to change anything in Homebrew, but in a branch of tap, in the [OSGeo/homebrew-osgeo4mac](https://github.com/OSGeo/homebrew-osgeo4mac) tap. I did the following.

First, I create a branch and checked out on it.

<pre class="lang:sh decode:true">$ cd /usr/local/Homebrew/Library/Taps/osgeo/homebrew-osgeo4mac
$ git checkout -b QGIS2.18.17
$ brew edit qgis2</pre>

Now you can edit the formula to be able to install QGIS 2.18.17. You can see how I modify [mine](https://github.com/luisspuerto/homebrew-osgeo4mac/blob/QGIS2.18.17/Formula/qgis2.rb) below that has highlighted the lines I&#8217;ve changed.

<pre class="nums:true lang:ruby mark:12-13 decode:true" title="qgis2.rb" data-url="https://raw.githubusercontent.com/luisspuerto/homebrew-osgeo4mac/QGIS2.18.17/Formula/qgis2.rb"></pre>

Now you just commit. 

<pre class="lang:sh decode:true">$ git stage Formula/qgis2.rb
$ git commit -m "qgis update to 2.18.17"</pre>

And now you just reinstall QGIS and move to `/Applications`:

<pre class="lang:sh decode:true">$ brew reinstall qgis2 --with-grass --with-saga-gis-lts
$ mv -f /usr/local/Cellar/qgis2/2.18.17/QGIS.app /Applications
$ ln -s /Applications/QGIS.app /usr/local/Cellar/qgis2/2.18.17/QGIS.app</pre>

<div id="attachment_1599" style="width: 494px" class="wp-caption aligncenter">
  <a href="http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.27.28.png"><img class="size-full wp-image-1599" src="http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.27.28.png" alt="" width="484" height="249" srcset="http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.27.28.png 484w, http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.27.28-300x154.png 300w" sizes="(max-width: 484px) 100vw, 484px" /></a>
  
  <p class="wp-caption-text">
    QGIS 2.18.17 splash screen
  </p>
</div>

<div id="attachment_1600" style="width: 1690px" class="wp-caption aligncenter">
  <a href="http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.27.49.png"><img class="size-full wp-image-1600" src="http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.27.49.png" alt="" width="1680" height="1050" srcset="http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.27.49.png 1680w, http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.27.49-300x188.png 300w, http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.27.49-768x480.png 768w, http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.27.49-1024x640.png 1024w, http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.27.49-1216x760.png 1216w, http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.27.49-400x250.png 400w" sizes="(max-width: 1680px) 100vw, 1680px" /></a>
  
  <p class="wp-caption-text">
    QGIS 2.18.17 interface
  </p>
</div>

## <span class="js-issue-title">Matplotlib</span> {.gh-header-title}

Since <span class="author"><a class="url fn" href="https://github.com/Homebrew" rel="author">Homebrew</a></span><span class="path-divider">/</span><a href="https://github.com/Homebrew/homebrew-science" data-pjax="#js-repo-pjax-container">homebrew-science</a>** **has been deprecated and some of the formulae wasn&#8217;t migrated to the <span class="author"><a class="url fn" href="https://github.com/Homebrew" rel="author">Homebrew</a></span><span class="path-divider">/</span><a href="https://github.com/Homebrew/homebrew-core" data-pjax="#js-repo-pjax-container">homebrew-core</a> this left us with a message at the end of the QGIS&#8217; compilation asking us to install [matplotlib](https://matplotlib.org) via pip.

<pre class="lang:sh decode:true ">The following Python modules are needed by QGIS during run-time:

    matplotlib, pyparsing

You can install manually, via installer package or with `pip` (if availble):

    pip install &lt;module&gt;  OR  pip-2.7 install &lt;module&gt;</pre>

I had a problem and when I tried to install using `pip install matplotlib` threw me an error, though.

<pre class="lang:sh decode:true ">Collecting matplotlib
  Using cached matplotlib-2.2.0-cp27-cp27m-macosx_10_6_intel.macosx_10_9_intel.macosx_10_9_x86_64.macosx_10_10_intel.macosx_10_10_x86_64.whl
Requirement already satisfied: subprocess32 in /usr/local/lib/python2.7/site-packages (from matplotlib)
Requirement already satisfied: backports.functools-lru-cache in /usr/local/lib/python2.7/site-packages (from matplotlib)
Requirement already satisfied: pyparsing!=2.0.4,!=2.1.2,!=2.1.6,&gt;=2.0.1 in /usr/local/lib/python2.7/site-packages (from matplotlib)
Requirement already satisfied: cycler&gt;=0.10 in /usr/local/lib/python2.7/site-packages (from matplotlib)
Requirement already satisfied: numpy&gt;=1.7.1 in /usr/local/lib/python2.7/site-packages (from matplotlib)
Requirement already satisfied: kiwisolver&gt;=1.0.1 in /usr/local/lib/python2.7/site-packages (from matplotlib)
Requirement already satisfied: pytz in /usr/local/lib/python2.7/site-packages (from matplotlib)
Requirement already satisfied: python-dateutil&gt;=2.1 in /usr/local/lib/python2.7/site-packages (from matplotlib)
Requirement already satisfied: six&gt;=1.10 in /usr/local/lib/python2.7/site-packages (from matplotlib)
Requirement already satisfied: setuptools in /usr/local/lib/python2.7/site-packages (from kiwisolver&gt;=1.0.1-&gt;matplotlib)
Installing collected packages: matplotlib
Exception:
Traceback (most recent call last):
  File "/usr/local/lib/python2.7/site-packages/pip/basecommand.py", line 215, in main
    status = self.run(options, args)
  File "/usr/local/lib/python2.7/site-packages/pip/commands/install.py", line 342, in run
    prefix=options.prefix_path,
  File "/usr/local/lib/python2.7/site-packages/pip/req/req_set.py", line 784, in install
    **kwargs
  File "/usr/local/lib/python2.7/site-packages/pip/req/req_install.py", line 851, in install
    self.move_wheel_files(self.source_dir, root=root, prefix=prefix)
  File "/usr/local/lib/python2.7/site-packages/pip/req/req_install.py", line 1064, in move_wheel_files
    isolated=self.isolated,
  File "/usr/local/lib/python2.7/site-packages/pip/wheel.py", line 345, in move_wheel_files
    clobber(source, lib_dir, True)
  File "/usr/local/lib/python2.7/site-packages/pip/wheel.py", line 323, in clobber
    shutil.copyfile(srcfile, destfile)
  File "/usr/local/Cellar/python@2/2.7.14_3/Frameworks/Python.framework/Versions/2.7/lib/python2.7/shutil.py", line 97, in copyfile
    with open(dst, 'wb') as fdst:
IOError: [Errno 13] Permission denied: '/usr/local/lib/python2.7/site-packages/matplotlib/legend.pyc'</pre>

Basically I had a ownership problem, that can be solve using `sudo`.

<pre class="lang:sh decode:true">$ sudo pip install matplotlib</pre>

Or fixing the [ownership](https://stackoverflow.com/questions/27870003/pip-install-please-check-the-permissions-and-owner-of-that-directory) like this:

<pre class="lang:sh decode:true">$ sudo chown -R $(echo $USER) ~/Library/Caches/pip</pre>

Now, I was able to install.

# How to install QGIS 3?

Well, this is a little bit more challenging, but just a little bit. The only version of a Homebrew formula that is available to install QGIS 3 is the developer version one that is in this <span class="author"><a class="url fn" href="https://github.com/qgis" rel="author">qgis</a></span><span class="path-divider">/</span><a href="https://github.com/qgis/homebrew-qgisdev" data-pjax="#js-repo-pjax-container">homebrew-qgisdev</a> repo-tap. So I just have to tap that repo:

<pre class="lang:sh decode:true ">$ brew tap qgis/homebrew-qgisdev</pre>

However, I needed to make changes in the formula to be able to install because it has a dependency on Matplotlib on <span class="author"><a class="url fn" href="https://github.com/Homebrew" rel="author">Homebrew</a></span><span class="path-divider">/</span><a href="https://github.com/Homebrew/homebrew-science" data-pjax="#js-repo-pjax-container">homebrew-science</a> and as we learned before that formula doesn&#8217;t exist anymore. In this case, it isn&#8217;t like in the QGIS 2, that it builds anyway and then ask you to install Matplotlib. It just doesn&#8217;t build and throws an [error](https://github.com/qgis/homebrew-qgisdev/issues/50).

I have two options here. I can just delete or comment the line where the dependency is called, or I can replace it for the legacy formula, which is located in the <span class="author"><a class="url fn" href="https://github.com/brewsci" rel="author">brewsci</a></span><span class="path-divider">/</span><a href="https://github.com/brewsci/homebrew-science" data-pjax="#js-repo-pjax-container">homebrew-science</a> tap. Either way I proceeded in the same way as I did previously.

First, I created a branch and edited the formulae.

<pre class="lang:sh decode:true">$ cd /usr/local/Homebrew/Library/Taps/qgis/homebrew-qgisdev
$ git checkout -b matplotlib-fix
$ brew edit qgis3-dev
</pre>

You can check how I&#8217;ve edited mine below. The important part is in the highlighted 86-87 lines.

<pre class="nums:true lang:ruby mark:86-87 decode:true" title="qgis3-dev.rb" data-url="v/blob/f8c5058e533424228ff26c1dabaf69c4cef00ad5/Formula/qgis3-dev.rb"></pre>

Then, I just committed my edits 

<pre class="lang:sh decode:true ">$ git add Formula/qgis3-dev.rb
$ git commit -m "fix for matplotlib"</pre>

Before I tried to build I have to do two more tweaks to be able to compile without [errors](https://github.com/qgis/homebrew-qgisdev/issues/66#issuecomment-372069535). First I have to reinstall [Bison](https://www.gnu.org/software/bison/).

<pre class="lang:sh decode:true">$ brew reinstall bison</pre>

And then I have to modify the file `/usr/local/bin/pyrcc5` and change `pythonw2.7` to `python3`.

<pre class="lang:sh highlight:0 decode:true" title="pyrcc5">#!/bin/sh
exec python3 -m PyQt5.pyrcc_main ${1+"$@"}</pre>

Now I could build QGIS 3 developer edition:

<pre class="lang:sh decode:true">$ brew install --no-sandbox qgis3-dev --with-grass --with-saga-gis-lts</pre>

<div id="attachment_1596" style="width: 494px" class="wp-caption aligncenter">
  <a href="http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.27.58.png"><img class="wp-image-1596 size-full" src="http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.27.58.png" alt="" width="484" height="249" srcset="http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.27.58.png 484w, http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.27.58-300x154.png 300w" sizes="(max-width: 484px) 100vw, 484px" /></a>
  
  <p class="wp-caption-text">
    QGIS 3 dev splash screen
  </p>
</div>

<div id="attachment_1597" style="width: 1690px" class="wp-caption aligncenter">
  <a href="http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.28.22.png"><img class="wp-image-1597 size-full" src="http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.28.22.png" alt="" width="1680" height="1050" srcset="http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.28.22.png 1680w, http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.28.22-300x188.png 300w, http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.28.22-768x480.png 768w, http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.28.22-1024x640.png 1024w, http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.28.22-1216x760.png 1216w, http://luisspuerto.net/wp-content/uploads/2018/03/Screen-Shot-2018-03-12-at-10.28.22-400x250.png 400w" sizes="(max-width: 1680px) 100vw, 1680px" /></a>
  
  <p class="wp-caption-text">
    QGIS 3 interface
  </p>
</div>

After I finished building the QGIS 3, I moved the app from the Homebrew Cellar to the `/Applications` folder in the same way I moved QGIS 2, but since I want to keep QGIS 2 and QGIS 3, in the process I renamed the app to `QGIS 3.app`.

<pre class="lang:sh decode:true ">$ mv /usr/local/opt/qgis3-dev/QGIS.app /Applications/QGIS\ 3.app
$ ln -s /Applications/QGIS\ 3.app /usr/local/opt/qgis3-dev/QGIS.app</pre>

# Final thoughts

I hope that in the near future the situation improve and we can enjoy the last version of QGIS on macOS in a easier way. Perhaps even without needed to compile. However, in this very moment isn&#8217;t the case.

You have to take into account that with this method you are installing the QGIS 3 developer version, so probably isn&#8217;t going to be really stable, or perhaps you are going to have no problem. I guess that you can install the stable version using the QGIS3-dev formulae, you just have to change the values <span class="lang:default highlight:0 decode:true crayon-inline ">branch =></span>  to `"release-3_0"` in line 37 and `version` to `"3.0"` on line 38.

<pre class="nums:true lang:ruby mark:37-38 decode:true" title="qgis3-dev.rb" data-url="https://raw.githubusercontent.com/luisspuerto/homebrew-qgisdev/085a83ed859f2cba87b76245f031664f21523e60/Formula/qgis3-dev.rb"></pre>

Anyhow, I think I&#8217;m going to keep the 3.1 version for a while.

&nbsp;

&nbsp;