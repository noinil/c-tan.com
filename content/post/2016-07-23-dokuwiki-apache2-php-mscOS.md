+++ 
title = "Install Dokuwiki + apache2 + php on macOS"
description = "Notes of installing Dokuwiki + apache2 + php."
date = "2016-07-23T16:41:00"
categories = ["technique"]
tags = ["dokuwiki", "apache", "macOS", "php"]

slug = "dokuwiki-apache-php-macOS"
summary = "Notes of installing Dokuwiki + apache2 + php on macOS."
+++ 

I began to forget important things since I don't remember when.  Then I knew I
was not young any more.  This is frustrating.  But I'm happy, because it's time
to build a personal wiki!  Exciting, isn't it?

Among innumerous possibilities, I finally chose
[Dokuwiki](https://www.dokuwiki.org/).  It's as versatile as Mediawiki, and
meanwhile stupidly simple to control.

To use dokuwiki on macOS, one need two things: [apache](http://www.apache.org/)
and [php](http://www.php.net/).  Luckily in my case both of them have been
provided by the basic installation of macOS EI Capitan.

## Apache2 + PHP on macOS

Here I list only the modifications on the config files.

### Apache General Settings

Result from `diff /etc/apache2/httpd.conf /etc/apache2/httpd.conf.bak` is shown below:

```diff
160c160
< LoadModule vhost_alias_module libexec/apache2/mod_vhost_alias.so
---
> #LoadModule vhost_alias_module libexec/apache2/mod_vhost_alias.so

169c169
< LoadModule php5_module libexec/apache2/libphp5.so
---
> #LoadModule php5_module libexec/apache2/libphp5.so

181,184c181,182
< User ctan
< Group staff
---
> User _www
> Group _www

501c499
< Include /private/etc/apache2/extra/httpd-vhosts.conf
---
> #Include /private/etc/apache2/extra/httpd-vhosts.conf
```
    
The first and the last differences enable using virtual hosts.  The second one
turn php on.  The third modification gave me the authority to make changes to
local file system directly from inside Dokuwiki.
    
<br />
### Apache virtual hosts

I created two new virtual hosts other than the default one.

```bash
<VirtualHost *:80>
    ServerAdmin master@localtest.com
    DocumentRoot "/Users/ctan/Workspace/sites/localtest"
    ServerName localtest.com
    ServerAlias www.localtest.com
    ErrorLog "/private/var/log/apache2/localtest.com-error_log"
    CustomLog "/private/var/log/apache2/localtest.com-access_log" common
    <Directory "/Users/ctan/Workspace/sites/localtest">
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Order deny,allow
        Allow from all
        Require all granted
    </Directory>
</VirtualHost>

<VirtualHost *:80>
    ServerAdmin master@ctwiki.com
    DocumentRoot "/Users/ctan/Workspace/sites/wiki"
    ServerName ctwiki.com
    ServerAlias www.ctwiki.com
    ErrorLog "/private/var/log/apache2/ctwiki.com-error_log"
    CustomLog "/private/var/log/apache2/ctwiki.com-access_log" common
    <Directory "/Users/ctan/Workspace/sites/wiki">
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Order deny,allow
        Allow from all
        Require all granted
    </Directory>
</VirtualHost>

<VirtualHost *:80>
    ServerName localhost
    DocumentRoot /Library/WebServer/Documents/
</VirtualHost>
```

With this configuration, I have the default virtual host changed to "localtest"
(explained later in this post).  The second host is for dokuwiki.  The third one
is actually to give the name "localhost" back to the original default.

As described in `/etc/apache2/extra/httpd-vhosts.conf`:

> The first VirtualHost section is used for all requests that do not match a
> ServerName or ServerAlias in any <VirtualHost> block.


<br />
## Install Dokuwiki

I forked dokuwiki on [Github](https://github.com/splitbrain/dokuwiki), then
cloned it to local computer.  Installation was basically super user-friendly.
Just open http://www.ctwiki.com/install.php, and follow the instructions.
