+++ 
title = "Jupyter/IPython Notebook Theme and Chinese Fonts"
description = "A theme for Jupyter Notebook, including Chinese font settings."
date = "2015-10-25T10:10:00"
categories = ["technique"]
tags = ["jupyter", "ipython", "notebook", "python"]

slug = "jupyter-notebook-theme"
summary = "Here I present a theme for Jupyter Notebook, including Chinese font settings."
+++ 


## Background

After using tiddlywiki 5 for about three weeks, I finally gave up.  Tiddlywiki
is good, just not for users without any web developing experience.  It was
really painful for me to try every method to get all my $\LaTeX$ equations work.
Tiddlywiki has already included "KaTeX" plugin, which is however a very limited
support for $\LaTeX$ math.  Commands as easy as `align` or `\pmod` don't work in
KaTeX.  This really drove me crazy.

## Jupyter/IPython

Actually I've used IPython to take notes for several months.  It's really handy
and powerful.  Once I used the `interact` function to demonstrate MD data
analysis, my supervisor was "shocked" to see the interactive dynamic effects.

IPython is now separated into several new sub-projects.  The new project name is
"Jupyter".  I like it.  And it is still evolving very fast.

After abandon of tiddlywiki, I was considering making my IPython notebooks look
better.  Then I found some very beautiful
[themes](https://github.com/dunovank/jupyter-themes).  Although, I still would
like to make some minor changes, especially for Chinese fonts.

I have to confess that I totally have no idea about how to write CSS or HTML.
After changing every parameter to see the effect, finally I managed to make my
theme work.

## About the Chinese Fonts

Personally I like "Kaiti" (楷体) very much.  But this time I tried using
"Songti" (宋体) for normal font style.  For italic and bold fonts, I used
"Kaiti" and "Heiti" (黑体), respectively.

## Visual Effects

Screenshot of IPython notebook Chinese paragraphs:

![Figure: Notebook Chinese paragraphs.](/img/posts/screenshots/scrot_ipython_notebook_chinese_p.png)

Screenshot of IPython notebook paragraph with $\LaTeX$ math equations:

![Figure: Notebook math.](/img/posts/screenshots/scrot_ipython_notebook_latex_math.png)

Screenshot of IPython notebook code blocks:

![Figure: Notebook code blocks.](/img/posts/screenshots/scrot_ipython_notebook_code.png)


## Get the Code

Get the code from [gist](https://gist.github.com/noinil/75eb2c58507e749294e1).

---
Reference:

- https://github.com/dunovank/jupyter-themes
