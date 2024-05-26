---
title: 'Enable MathJax in Jekyll'
date: 2024-05-26
categories:
  - blog
tags:
  - jekyll
  - MathJax
---
This website is built on [Jekyll](https://jekyllrb.com/), a static site generator, and hosted on GitHub as a GitHub page. I had been enjoying playing around with this [stack](https://www.codecademy.com/resources/blog/tech-stack/) so far. However, recently I found out that the Markdown, the mark up languange used to generate the html pages used in Jekyll, is not equipped with `LaTex` or other features to generate mathematical symbols and equation.

I did some digging and found [Ian Goodfellow's](https://en.wikipedia.org/wiki/Ian_Goodfellow) blog [post](https://www.iangoodfellow.com/blog/jekyll/markdown/tex/2016/11/07/latex-in-markdown.html) on how to enable math rendering on html pages using a suite of javascript library called [MathJax](https://www.mathjax.org/). The steps outlined in Ian Goodfellow's blog post is pretty straight forward, but I am not a fan of adding custom scripts directly in the html page. Partly because I want to make sure my website is up to date with the jekyll theme source on GitHub. Adding a custom script directly to the layout html script will make updating and upgrading my setup a little trickier.

Fortunately, the [Minimal Mistake](https://mmistakes.github.io/minimal-mistakes/) theme that I use in my website has a dedicated placeholder for custom scripts. So here are the steps.

1. Locate the placeholder for header custom scripts. Technically, you do not need to add it on the header, but the MathJax documentation recommends adding it to the header. In my case the placeholder is in `_includes/head/custom.html`.

2. Copy paste the following lines

```bash
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>

<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
```

That's it. Once you've added those lines you can now render mathematical symbols using LaTex syntaxes. For example to generate something that looks like this \\(1+2+3+\cdots+10=\sum_{n=1}^{10}n\\), you need to type this in your markdwon file.

```bash
\\(1+2+3+\cdots+10=\sum_{n=1}^{10}n\\)
```

 I hope this is helpful, and let me know if you have any questions.