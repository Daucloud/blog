+++
date = '2025-04-15T21:49:15+08:00'
draft = false
title = '使用Latex Worksop时，如何引入Minted宏包？'
tags = ["Latex Workshop","VScode"]  
categories = ["Programming"]
+++
我习惯使用`VScode`(现在是`Cursor`)+[`Latex Workshop`](https://github.com/James-Yu/LaTeX-Workshop)工具链来书写`Latex`，但是最近引入`Minted`宏包时遇到了一些困难，并困扰了我一阵(详见[The latexmk recipe can find pygmentize, but the xelatex and pdflatex recipes cannot.](https://github.com/James-Yu/LaTeX-Workshop/discussions/4574))，因此打算记录一下。
1. `pip install Pygments`. `Minted`依赖`pygmentize`来提供语法高亮支持。
2. 打开`VScode`的`settings.json`，找到（或添加）`latex-workshop.latex.tools`配置，并在各个`tools`的`args`选项中添加`-shell-escape`选项。兹列出我的配置如下：
```json
{
  "latex-workshop.latex.tools": [
    {
      "name": "xelatex",
      "command": "/Library/TeX/texbin/xelatex",
      "args": [
        "-shell-escape",
        "-synctex=1",
        "-interaction=nonstopmode",
        "-file-line-error",
        "%DOCFILE%"
      ]
    },
    {
      "name": "pdflatex",
      "command": "/Library/TeX/texbin/pdflatex",
      "args": [
        "-synctex=1",
        "-interaction=nonstopmode",
        "-file-line-error",
        "-shell-escape",
        "%DOCFILE%"
      ]
    },
    {
      "name": "latexmk",
      "command": "latexmk",
      "args": [
        "-synctex=1",
        "-interaction=nonstopmode",
        "-file-line-error",
        "-pdf",
        "-xelatex=xelatex -shell-escape %O %S",
        "-outdir=%OUTDIR%",
        "%DOC%"
      ]
    },
    {
      "name": "bibtex",
      "command": "/Library/TeX/texbin/bibtex",
      "args": ["%DOCFILE%"]
    }
  ],
  "latex-workshop.latex.recipes": [
    {
      "name": "XeLaTeX",
      "tools": ["xelatex"]
    },
    {
      "name": "PDFLaTeX",
      "tools": ["pdflatex"]
    },
    {
      "name": "BibTeX",
      "tools": ["bibtex"]
    },
    {
      "name": "LaTeXmk",
      "tools": ["latexmk"]
    },
    {
      "name": "xelatex -> bibtex -> xelatex*2",
      "tools": ["xelatex", "bibtex", "xelatex", "xelatex"]
    },
    {
      "name": "pdflatex -> bibtex -> pdflatex*2",
      "tools": ["pdflatex", "bibtex", "pdflatex", "pdflatex"]
    }
  ]
}
```
> 务必确保`--shell-escape`选项在`%DOCFILE%`或`%DOC%`之前！