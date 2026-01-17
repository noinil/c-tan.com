+++
title = "Deploying Pelican Blogs on VPS with Git"
description = "Build simple blogs with pelican on VPS with git."
date = "2016-04-26T18:05:00"
categories = ["technique"]
tags = ["pelican", "git"]

slug = "pelican-vps-git"
summary = "A simple way to use git to organize Pelican websites on VPS."
+++


**Notice**

The method shown here is almost the same as described in
[this page](https://www.digitalocean.com/community/tutorials/how-to-deploy-jekyll-blogs-with-git),
which is a tutorial for deploying Jekyll on DigitalOcean.  I only made very
minor changes so that it worked for pelican.


## About static site generators

Pelican is a tool that generates static HTML sites from *reStructuredText* or
*Markdown* files.[^1] Similar tools are *Jekyll*,[^2] *hugo*,[^3] *hexo*,[^4]
and *Nikola*.[^5] The common advantage of these tools is that the generated web
site is portable, fast, staic, and most importantly, easy to organize with
version control systems.  Among them, Jekyll is nowadays the most popular one.
I also used it to write my blog in the last several years.  However, I knew
very little about Ruby, in which Jekyll is witten.  This raised a lot of pains
when I was using Jekyll.  Finally I decided to turn to Pelican, which is
developed with Python.

While using Jekyll, I used to keep the files in a Git repository, so that I
could edit them and run local tests.  After testing, the generated site files
can be deplyed to VPS using git via ssh.  Alternatively, raw content files can
be git pushed to VPS and compiled there.  For pelican, the same strategy can be
used.


## Installation

Basically, we want the environments be (if possible) exactly the same on VPS and
local machine.  Although, in my case even the python versions were different.
Luckily I didn't encounter any weird problem.  Now let's first install pelican,
of course:

```bash
pip3 install pelican
```

Add `sudo` if necessary.  For me the python3 version of pelican works well.

As suggested by pelican official website, I also installed *typogrify* and
*beautifulsoup4*:

```bash
pip3 install typogrify beautifulsoup4
```

Besides, some of the pelican plugins and themes are very nice and handy.  They
can be easily found on Github.  Usage of these stuffs are beyond the topic of
this post, and thus will not be discussed here.


## Setting up git on VPS and local machine

Basically, I followed
[this page](https://www.digitalocean.com/community/tutorials/how-to-deploy-jekyll-blogs-with-git)
to set up git for jekyll blogs.  Pelican can be done in the same way.  The key
point is to create a `post-receive` git hook.

### VPS settings

First create a git repository on VPS:

```bash
mkdir working_dir && cd working_dir
git init --bare
cd hooks
touch post-receive
chmod +x post-receive
```

Add the following contents to the `post-receive` file:

```bash
#!/bin/bash -l
GIT_REPO=$HOME/working_dir
TMP_GIT_CLONE=$HOME/tmp_dir
PUBLIC_WWW=/var/www/site_output_dir

git clone $GIT_REPO $TMP_GIT_CLONE
cd $TMP_GIT_CLONE
pelican content -s publishconf.py -o $PUBLIC_WWW
cd ..
rm -Rf $TMP_GIT_CLONE
exit
```


### Local settings

On the local machine, add VPS as a remote to the git repository:

```bash
git remote add vps ssh://username@VPS_IP:PORT_NUM/path/to/working_dir
```

Once the commits are pushed to remote, the site will be generated automatically.



[^1]: https://docs.getpelican.com/

[^2]: https://jekyllrb.com/

[^3]: https://gohugo.io/

[^4]: https://hexo.io/

[^5]: https://getnikola.com/
