---
layout:            post
title:             "JekyllDecent Installation"
date:              2016-08-11
tags:              Jekyll Theme GitHub
category:          Jekyll
author:            hongyuan

---

[JekyllDecent](https://github.com/jwillmer/jekyllDecent) is a very awesome blog template that has been used to generated this blog. You can find its demo page [here](http://jwillmer.de/). This post is just a summary of [this post](http://jwillmer.github.io/jekyllDecent/blog/readme/Readme) and [this post](http://jwillmer.de/blog/tutorial/how-to-install-jekyll-and-pages-gem-on-windows-10-x46) (written by Jens Willmer, the author of jekyllDecent) so that I can quickly set up jekyllDecent on Windows anytime simply by reading through.


## Prerequisites
* Git
* A Github account

If you don't know much about Git, have a look at [Getting Started with GitHub Desktop](https://help.github.com/desktop/guides/getting-started/). To install Git, simply click [here](https://git-scm.com/download/win) and the download will start automatically. Then just open the file follow the instructions.

## Installation


**1. Install Chocolatey**{:.em-uln}

Open an **administrative** command prompt (right click on cmd.exe and choose 'Run As Administrator') and run:

```
@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
```

**2. Install Ruby 2.2.4**{:.em-uln}

```
choco install ruby -version 2.2.4 -y
```

**3. Install Ruby DevKit**{:.em-uln}

Run the following command (NOTE that it will fail due to the ruby path cannot be found but this command will automatically download Ruby DevKit in C:\tools\DevKit2 for you):

```
choco install ruby2.devkit -y
```

Append the root directory of  Ruby to the config file:

```
echo. >> C:\tools\DevKit2\config.yml && echo - C:\tools\ruby22 >> C:\tools\DevKit2\config.yml
```

Reopen another command prompt, navigate to C:\tools\DevKit2 and install Ruby DevKit:

```
cd C:\tools\DevKit2
ruby dk.rb install
```

**4. Install Jekyll and bundler**{:.em-uln}

Now reopen the command prompt again and enter:

```
gem install jekyll
gem install bundler
```

If it says "cerrificate verify failed", you may want to use http temporarily instead of https:

```
gem sources --remove https://rubygems.org/
gem sources -a http://rubygems.org/
```

and switch back after you finish the installation:

```
gem sources --remove http://rubygems.org/
gem sources -a https://rubygems.org/
```

**5. Clone, build and serve**{:.em-uln}

```
git clone https://github.com/jwillmer/jekyllDecent.git
cd jekyllDecent
bundle install
bundle exec jekyll serve
```
