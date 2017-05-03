+++ 
title = "Personal Chatting Bot for Wechat"
description = "Use wxBot to build a Chatting bot for Wechat."
date = "2016-10-02T11:10:00"
categories = ["technique"]
tags = ["python2"]

slug = "wechat-bot"
summary = "Build a Chatting bot for Wechat with the wxBot package."
+++ 

Wechat is a stupid chatting program in China.  Although I don't like it very
much, many of my relatives and friends use this program.  Therefore sometimes I
have to deign to suffer it.

Luckily, I found a very nice project which provides user-friendly API for
Wechat.  With this project I could deploy a bot on my server and intelligently
answer questions when I'm away from keyboard (phone).  The project
is [wxBot](https://github.com/liuwons/wxBot).

I added my own settings on top of wxBot, and my bot can leave a message when I
am called while afk. 

I also integrated the functions of the Linux tool `fortune` into my bot.  My
friends can use fortune from wechat now.

Most interestingly, I wrote a simple python function to automatically recite
poems when activated by a contact, even within "group chatting".

For more details, please see the Chinese version of this post.

My bot can be found [here](https://github.com/noinil/wxBot/blob/localtest/wechat_bot_ct.py).
