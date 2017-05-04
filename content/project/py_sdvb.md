+++
date = "2017-05-04T18:17:58+09:00"
external_link = ""
highlight = true
image_preview = "projects/pysdcv_preview.png"
math = false
summary = "Yet another dictionary tool written in python. Allow regex."
tags = ["python"]
title = "py_sdvb"

[header]
  caption = ""
  image = ""

+++

[Source code deployed on Github.](https://github.com/noinil/py_sdvb)


## Description

- Vocabulary builder by generating interesting word lists from stardict
  dictionaries.  Allowing **regular expression** search.  Written in python.
- Command-line dictionary with colored output.


## Features

### Regular expression support

See some *regex* search examples below:

![Regex support.](/img/projects/pysdcv_regex.png)

### Colored output

Keywords are highlighted.

<img src="/img/projects/pysdcv_coloroutput.png"  width="800">

### Jupyter/Ipython notebook (Experimental)

Interactively searching/translating in Jupyter notebooks.

<img src="/img/projects/pysdcv_ipynb_sdcv.gif"  width="600">


<br />
## Installation

Py-sdvb is now available on PyPI.  Use `pip` (python3 version) to install:

```bash
pip3 install py-sdvb
```

### Prerequisites for manual installation

This package is based on `PyStarDict` (https://github.com/lig/pystardict).

Programs are tested for python3 on Mac OS X.


<br />
## Usage

### Get the dictionaries

Put all the dictionaries in the directory `~/.stardict/`.  If you have been
using tools like *stardict* or *sdcv*, you probably have already had the
dictionaries stored there.

### pysdvb

This is the primary program to help building your vocabulary, by generating a
list of words together with their translations.

Run `pysdvb` like this:
```bash
pysdvb [-s] [-h]
```

Use the `[-h]` option for help.

The `-s` option provides a mode to choose dictionary.  Otherwise, all the
dictionaries are used.

You will then be prompted with `REGEX:`, which askes you to input **regular
expressions** for search.



### pysdcv

This is an interactive command line dictionary tool, basically an analog of
*sdcv*.

```bash
pysdcv
```

### Manually add to `$PATH`

If py_sdvb is not installed with `pip`, probably you need to run these commands: 

```bash
ln -s /path/to/py_sdvb/pysdvb/sdvb.py /usr/local/bin/pysdvb
ln -s /path/to/py_sdvb/pysdvb/sdcv.py /usr/local/bin/pysdcv
```
