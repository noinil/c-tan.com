+++
date = "2015-05-04T18:17:10+09:00"
external_link = ""
highlight = true
image_preview = "projects/cafemol-mode_preview.png"
math = false
summary = "An Emacs major mode for editing CafeMol input file."
tags = ["biophysics", "molecular dynamics", "emacs"]
title = "cafemol-mode"

[header]
  caption = ""
  image = ""

+++

{{< figure src="/img/projects/cafemol-mode.png" title="CafeMol-mode in Emacs." >}}

[Source code deployed on Github.](https://github.com/noinil/cafemol-mode)

## Usage:
To use this mode for writing or editing CafeMol input file, add the following to
your Emacs configuration file:

```lisp
(add-to-list 'load-path "~/path_to_cafemol-mode/")
(require 'cafemol-mode)
(add-to-list 'auto-mode-alist '("\\.inp\\'" . cafemol-mode))
```

To use the snippets, please try this: https://github.com/noinil/esnippets

## Features:
- [X] provide a major mode for CafeMol input files
- [X] key words highlighting
- [X] comment (`*`) recognition;  use `Alt`+`;` to comment out selected region
- [ ] indentation
- [ ] section fold
- [X] snippet
- [ ] other...

## Commentary
This file is derived from *Emacs DerivedMode*:
http://www.emacswiki.org/emacs/DerivedMode

As for syntax highlighting, the codes are modified from Xah Lee's tutorial
(*ergoemacs*): http://ergoemacs.org/emacs/elisp_syntax_coloring.html
