+++ 
title = "用 LuaTeX 排版日文歌曲与中文翻译  (东京爱情故事)"
description = "尝试排版双栏歌曲及翻译."
date = "2016-08-02T11:17:00"
categories = ["technique"]
tags = ["LaTeX", "Japanese"]

slug = "Tokyo-love-story-lyrics-LuaTeX"
summary = "这是我首次尝试用 LuaTex排版双栏日语歌曲及中文翻译."
highlight_languages = ["tex"]
isCJKLanguage = true
+++ 

最近明明在跟日语老师学习歌曲， 我于是领到一个排版打印的任务。  事实上由于高田さん
只用微软 Word 来修改文章， 我已经被迫停用 $\TeX$ 好久了。这正是一个重拾的好机会。
从前我排版中文文章的时候都用 `XeTeX` + `cTeX`，然而它对日文的支持据说并不如意。
另外 一个可选是`pTeX`，不过好像编码设置有障碍。 最终我选择 `LuaTeX` 是因为看起来
可以利用 `Lua` 做大量扩展。 不过以我的实力和精力大概永远不会自己弄什么扩展吧囧。
在这篇文章里我展示一个简单的实例， 是我一个晚上的一点微小的成果。


<br />
## 包
---

使用到三个包：

- [`luatexja`](http://www.luatex.org/): 这个是在 `LuaTeX` 中排版日文的基本包
- `luatexja-fontspec`: 可以方便地设置字体 （顺便也包含一下中文字体设置）
- [`pxrubrica`](https://www.ctan.org/tex-archive/language/japanese/pxrubrica):
  这个神奇的包可以实现给漢字加上假名注音
  
<br />
## 示例
---

这个例子中展示的是日语歌曲 "ラブ・ストーリーは突然に" (东京爱情故事的主题曲) 和
它的中文翻译。  我使用了表格来让它们看起来更整齐。

```tex
\documentclass[a4paper, 11pt]{article}

\usepackage[top=1in, bottom=1.1in, left=1.2in, right=1.2in, a4paper]{geometry}
\usepackage{pxrubrica}
\usepackage{luatexja}
\usepackage{luatexja-fontspec}
\usepackage{setspace}
\setmainjfont{Kaiti SC}

\title{\vspace{-2em}東京ラブストーリー  \\  ラブ  \cdot ストーリーは突然に}
\date{ \vspace{-1.5em}}
\author{詞曲：小田和正 \\   翻譯：谭丞 \quad noinil@gmail.com}

\begin{document}

\onehalfspacing
\maketitle
\doublespacing

\begin{tabular}{rl}
    \jruby{何}{なん} から  \jruby{伝}{つた} えればいいのか & 不知该从何说起  \\
    \jruby{分}{わ} からないまま  \jruby{時}{とき} は  \jruby{流}{なが} れて & 时间消逝无声息  \\
    \jruby{浮}{う} かんでは  \jruby{消}{き} えてゆく & 脑中光影来又去  \\
    ありふれた  \jruby{言葉}{こと|ば} だけ  & 心头言语总难题 \\
    \jruby{君}{きみ} があんまり  \jruby{素敵}{す|てき} だから & 你这样美丽动人  \\
    ただ  \jruby{素直}{す|なお} に \  \jruby{好}{す} きと  \jruby{言}{い} えないで & 我不敢表明心意  \\
    \jruby{多分}{た|ぶん} もうすぐ  & 一切不须再多言 \\
    \jruby{雨}{あめ} も \jruby{止}{や} んで  \jruby{二人}{ふた|り} たそがれ & 黄昏将近雨淋漓  \\[1em]
\end{tabular}
\ldots

\end{document}
```

输出效果是这样的：

![Tokyo love story lyrics typesetting](/img/posts/screenshots/scrot_Tokyo_love_story_pdf.png)

需要完整歌词和翻译的朋友请给我写邮件私下讨论。
