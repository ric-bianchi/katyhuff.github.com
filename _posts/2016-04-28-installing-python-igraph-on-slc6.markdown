---
layout: post
title: Installing python-igraph on SLC6 with Conda
date: '2016-04-28 13:39:21'
tags:
- data-visualization
- conda
- sysadmin
- python
---


### Installing python-igraph

The usual conda command to install new packages:

```bash
conda install igraph
```

gave not results.

So we searched for a recipe for Linux 64 on anaconda.org (install the anaconda client with `conda install anaconda-client` if not done already):

```bash
$ anaconda search -t conda python-igraph
Using Anaconda Cloud api site https://api.anaconda.org
Run 'anaconda show <USER/PACKAGE>' to get more details:
Packages:
     Name                      |  Version | Package Types   | Platforms      
     ------------------------- |   ------ | --------------- | ---------------
     FSQMcAfee/python-igraph   |      0.7 | conda           | osx-64         
                                          : High performance graph data structures and algorithms
     anjentai/python-igraph    |      0.7 | conda, pypi     | win-32         
                                          : High performance graph data structures and algorithms
     bioconda/python-igraph    | 0.7.1.post6 | conda           | linux-64       
                                          : High performance graph data structures and algorithms
     cmckeague/python-igraph   | 0.7.1.post6 | conda           | linux-64, linux-armv7l, osx-64
                                          : High performance graph data structures and algorithms
...
```

we pick the 'bioconda' package and we run 'show' on it to get the full installation command:

```bash
$ conda info bioconda/python-igraph
Using Anaconda Cloud api site https://api.anaconda.org
Fetching package metadata: ....
Error:  Package missing in current linux-64 channels: 
  - bioconda/python-igraph
(visualization)[rbianchi@rbianchi-pitt] ~ $ anaconda show bioconda/python-igraph
Using Anaconda Cloud api site https://api.anaconda.org
Name:    python-igraph
Summary: High performance graph data structures and algorithms
Access:  public
Package Types:  conda
Versions:
   + 0.7.1.post6

To install this package with conda run:
     conda install --channel https://conda.anaconda.org/bioconda python-igraph
```

We then install the package using the suggested command:

```bash
conda install --channel https://conda.anaconda.org/bioconda python-igraph
```

And then we test that the installation went fine:

```bash
python
Python 2.7.11 |Continuum Analytics, Inc.| (default, Dec  6 2015, 18:08:32) 
[GCC 4.4.7 20120313 (Red Hat 4.4.7-1)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
Anaconda is brought to you by Continuum Analytics.
Please check out: http://continuum.io/thanks and https://anaconda.org
>>> import igraph
>>>
```

**DONE!** The package could be imported, so the installation went fine.

----

### Installing Cairo (`py2cairo`)

Now we have to install the graphics library Cairo, which is used by igraph for screen rendering:

```bash
conda install py2cairo
```

----

### Trying ipgragh

We can now starting use the nice igraph package to draw our network graphs:

```python
import igraph as ig
g = ig.Graph.Tree(127, 2)
layout = g.layout("kk")
ig.plot(g, layout = layout)
```
![](/gblog/content/images/2016/04/igraphzwzS8p.png)

Good!! :-)





