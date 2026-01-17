+++ 
title = "Typesetting Lyrics of Japanese/Chinese Songs with LuaTeX"
description = "A simple example of typesetting Japanese Lyrics with translations."
date = "2016-08-02T11:17:00"
categories = ["technique"]
tags = ["LaTeX", "Japanese"]

slug = "Tokyo-love-story-lyrics-LuaTeX"
summary = "In this post I present an example of typesetting Japanese Lyrics with Chinese translations using LuaTex."
highlight_languages = ["tex"]
+++ 

I was asked by my wife to typeset a Japanese song together with the Chinese
translations.  Then I picked up $\TeX$ again.  Previously, I used to employ
`XeTeX` and `cTeX` for documents written in Chinese.  But it was reported that
this combination performed poorly for Japanese typesetting.  I was also told a
better choice could be the `LuaTeX`.  Then I tried it yesterday evening.
Basically I didn't feel any pain in using it.  All the syntax I needed wass
totally same as normal $\LaTeX$.  Here I show an example of writing Japanese and
Chinese in one document with `LuaTeX`.


## Packages

I used three packages for Japanese (and Chinese):

- `luatexja`: the basic package for ja supports;
- `luatexja-fontspec`: the package that enables specific font settings (also
  works for Chinese language);
- `pxrubrica`: the package that provides the function to add furigana (ふりがな) above to
  kanji (漢字).
  
## Example Document

In this document I tried to translate a Japanese song "ラブ・ストーリーは突然に"
into Chinese.  This song was used as the theme song for the famous drama "Tokyo
Love Story" (東京ラブストーリー).

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

Here is the pdf output:

![Tokyo love story lyrics typesetting](/img/posts/screenshots/scrot_Tokyo_love_story_pdf.png)
