# Resume

我的简历与生成工具
==================

我的简历：

本工具最新版去掉了 mwhite 版核心的 resume.py，几乎已经与其没有关系，新的流程完全使用 pandoc 的使用惯例，并大幅改进了 LaTeX 的模板与 html 的样式。

[在线版](http://yarray.github.io/resume)

My resume with both [online]() and [pdf]() versions (from LaTeX) using [mwhite's template](https://github.com/mwhite/resume) (outdated)
=======
This is a simple Markdown resumé template, LaTeX header, and pre-processing
script that can be used with [Pandoc](http://johnmacfarlane.net/pandoc/) to generate
professional-looking PDF and HTML output.

## Dependencies
===============

* Pandoc >= 1.9 
* Python >= 2.7
* A Tex installation with pdflatex and the Tex Gyre Pagella font, and some
  packages needed by pandoc.

## Usage
=======

To generate PDF and HTML versions of each .md file in the directory:

    $ make
