+++ 
title = "Chinese 12-Year-Cycle Animals as Org-mode Heading Icons"
description = "Use emoji for Emacs Org-mode heading icons."
date = "2015-09-27T20:00:00"
categories = ["technique"]
tags = ["emacs", "org-mode"]

slug = "emacs-animal-org-bullet"
summary = "An idea of use interesting emoji as Emacs Org-mode heading icons."
highlight_languages = ["lisp"]
+++ 


## Ideas about Org-mode Headings

As an outlining tool, Emacs org-mode has a hierarchical organization of
contents.  Headings, as the scaffold of a document, should be descriptive,
prominent, and expressive.  There are several ways to make headings conspicuous:

- large, bold fonts
- light colors
- strange (I mean good-looking) org-bullets.[^1]

Recently I changed my org-bullets to Chinese 12-year-cycle animals, which are
well-known by every person in China.  Not only being interesting, but this
setting is also very convenient for me since I can easily tell in which level
I'm currently writing.  Well, this actually depends on how familiar with the
animals you are.

<!-- ![Figure: animal org-bullets.](/img/posts/screenshots/scrot_2015-09-27.png) -->
<img src="/img/posts/screenshots/scrot_2015-09-27.png" alt="Figure: animal org-bullets." style="width: 400px;"/>

---

## Customization of Emacs

After installing org-bullets, set the variable `org-bullets-bullet-list` through
`M-x customize`, or add this to your configuration file:

```lisp
(custom-set-variables '(org-bullets-bullet-list (quote ("🐭" "🐮" "🐯" "🐰" "🐲" "🐍" "🐴" "🐑" "🙉" "🐔" "🐶" "🐷"))))
```

---

## How to Memorize the 12-Animal Cycle

As far as I can remember, when I was 5 years old, my mother taught me to recite
the animal cycle four-by-four:

> 鼠牛虎兔，
> 龙蛇马羊，
> 猴鸡狗猪。

But I prefer this way:

> 鼠牛虎，
> 兔龙蛇，
> 马羊猴，
> 鸡狗猪。


[^1]: [https://github.com/sabof/org-bullets](https://github.com/sabof/org-bullets)
