+++
title = "Local Network IRC Server + Web IRC Client + IRC Bot"
description = "One simple way to build local IRC chatting server."
date = "2015-10-10T10:10:00"
categories = ["technique"]
tags = ["archlinux", "linux", "irc"]

highlight_languages = ["javascript"]
slug = "irc-server-setup"
summary = "Notes of how to build an local IRC chatting server for small group."
+++

## Background and Motivation

Japanese students are often too shy to talk in English, even those from Kyoto
University.  To keep in communication with them, I built an IRC (Internet Relay
Chat) server in the local network.  It's set up on an old PC, whose OS was
Archlinux, and kernel version was 4.2.2.  All the tools I used can be easily
installed through package managers like `pacman`, `pip`, and `npm`.

---

## STEP 1: IRC Server

In Archlinux official repositories there are many Internet Relay Chat servers.
Here I briefly describe how to install and configure the original IRC server
*ngircd*.[^1]

### Installation

To install ngircd, simply run the following command:

```bash
pacman -S ngircd
```

The configuration files are stored in `/etc/ngircd.conf`. The documents are
available in `/usr/share/doc/ngircd` directory.

### Configuration

The IRC settings can be done in the configuration file `/etc/ngircd.conf`.

You can set the IRC host information in this file like this:

```bash
[Global]
Name = irc.cafemol.net

AdminInfo1 = A.Einstein
AdminInfo2 = Room 201A
AdminEMail = abc@xyz.com

Info = Group chat place for CafeMol users.

Network = theory.biophys.kyoto-u.ac.jp
```


In the `[Limits]` block, some limits and timeouts for the ngIRCd instance are
defined.  You may have to change some of them:

```bash
[Limits]
ConnectRetry = 30

# Maximum number of channels a user can be member of (0: no limit):
MaxJoins = 10

# Maximum number of simultaneous connections from a single IP address
# the server will accept (0: unlimited):
MaxConnectionsIP = 50
```

Here I set `MaxConnectionsIP = 50` because I also built a web-based interface
for my IRC server, through which every group member can get access.  In this
case, a limit of larger than 30 (number of members in our group) is necessary.

The `[Operator]` section is used to define IRC Operator.  Change name and
password for the Op account.

```bash
[Operator]
Name = ein
Password = EIN
```

Optionally, one can also configure pre-defined channels in `[Channel]` sections.

### Run

Control the `ngircd` service through `systemd`:

```bash
systemctl enable ngircd.service
systemctl start ngircd.service
```


---

## STEP 2: Web IRC client

Again, there are many web based IRC clients.  Here I demonstrate the
installation and configuration of *shout*.[^2]

### Installation

The easiest way to install shout is using `npm`:

```bash
sudo npm install -g shout
```

### Configuration

In my case, the user configuration file is `~/.shout/config.js`.  Shout is
well-documented and thus easy to customize:

```javascript
module.exports = {
  public: true,
  host: "10.1.1.131",
  port: 9000,
  theme: "themes/zenburn.css",
  
  defaults: {
    name: "CafeMol",
    host: "10.1.1.131",
    port: 6667,
    nick: "cafemol-user",
    username: "cafemol-user",
    realname: "Cafe User",
    join: "#cafemol"
  },
};
```

### Run

```bash
shout start
```

Get access to shout through modern web browsers:
![Figure: Screenshot of shout web login interface.](/img/posts/screenshots/scrot_shout_login_2015-10-10.png)
![Figure: Screenshot of shout web chat interface.](/img/posts/screenshots/scrot_shout_chat1_2015-10-10.png)

As can be seen, utf-8 is supported.  One can type Chinese, Japanese, as well as
Emoji.  Also, figures can be shown directly in the chatting window.

---

## STEP 3: IRC Bot

*Sopel* is an easy-to-use IRC bot framework.  It's written in python, and thus
is highly extensible.[^3] With very basic knowledge of python, one can write
many interesting and handy extensions.

### Installation

Install sopel through `pip`:

```bash
pip install sopel
```

Luckily for Archlinux users, sopel has been packaged into the Archlinux
official `community` repository.

```bash
pacman -S sopel
```

### Configuration

Set name, nick, and host information for the bot through `~/.sopel/default.cfg`:

```bash
[core]
nick = lysa
host = 10.1.1.131
use_ssl = false
port = 6667
owner = tan
name = Lysa
channels = #cafemol,#biophysics

enable = search,calc,weather,countdown,tld,rand,dice,movie,seen,unicode_info
exclude = adminchannel,meetbot,help,etymology,twit,github,ip
prefix = \;
```


### Run

Usually I run two sopel services simultaneously, one for my local IRC server,
the other for freenode.net, using different configurations respectively.

```bash
sopel -c config.cfg
```

![Figure: Screenshot of shout IRC with sopel bot.](/img/posts/screenshots/scrot_sopel-2015-10-10.png)

[^1]: [http://ngircd.barton.de/](http://ngircd.barton.de/)
[^2]: [http://github.com/erming/shout](http://github.com/erming/shout)
[^3]: [http://github.com/sopel-irc/sopel](http://github.com/sopel-irc/sopel)
