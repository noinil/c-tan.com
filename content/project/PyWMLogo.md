+++
date = "2016-08-30T18:17:28+09:00"
external_link = ""
highlight = true
image_preview = "projects/PyWMLogo_preview.png"
math = true
summary = "A simple tool to plot information content LOGO for PWM.  Image format is svg."
tags = ["bioinformatics", "biophysics", "python", "svg"]
title = "PyWMLogo"

[header]
  caption = ""
  image = ""

+++

{{< figure src="/img/projects/PyWMLogo.png" title="PWM, PFM, and Content Logo." >}}

[Source code deployed on Github.](https://github.com/noinil/PyWMLogo)

## PyWMLogo

This project creates svg file of information content logo (or sequence logo)[^1] for PWM of DNA-binding proteins.

### Logo creation algorithm

Here we don't consider about the experiments like microarray binding assay, data
collection, and all other possible processes such as sequence alignments.  Our
assumption is that we already have the frequency matrix $M_f$.

The information content of position $i$ is given by[^2]:
$$ IC\_{i} = 2 + \sum f\_{b,i} \times \log\_2 f\_{b,i}$$
where $f_{a,i}$ is the relative frequency of base $b$ at position $i$.
Here we use $\log_2$ so that the information content is measured in bits.

In case that the expected average frequency of each base type is $25\%$,
information content can also be written as: 
$$IC\_i = \sum f\_{b,i}\times \log\_2 \frac{f\_{b,i}}{0.25}.$$

The height of letter $b$ in column $i$ is then given by
$$H\_{b,i} = f\_{b,i} \times IC\_i.$$

### Plotting

The figure format is svg.  All the information (height of base type characters) was "manually" written into the svg file.  



[^1]: [Sequence logo in Wikipedia.](https://en.wikipedia.org/wiki/Sequence_logo)
[^2]: Schneider, T. D., Stormo, G. D., Gold, L. and Ehrenfeucht, A.  Information content of binding sites on nucleotide sequences. *J. Mol. Biol.*, **1986**, 188, 415–431.
