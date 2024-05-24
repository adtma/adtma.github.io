---
title: 'Delta notations in MS Word'
date: 2024-05-22
categories:
  - blog
tags:
  - geology
  - writing
---
One of the most tedious parts of writing a manuscript in my field is the need to write geochemical notations. There is a lot of greek letters or mathematical symbols that we use as notations. One of the most of common notations that I use in my writing is the delta notation (\\( \delta \\)). For example, we use the notation \\(\delta^{13}C\\) to express the abundance of \\(^{13}C\\) relative to \\(^{12}C\\). The formula to convert isotopic ratios to delta notation is shown below. Geochemists express isotopic ratios in delta notation because this expression makes small numbers more convenient to read and display.

$$
\delta^{13}C = \left( \frac{^{13}C/^{12}C_{sample}}{^{13}C/^{12}C_{standard}} - 1 \right) \times 1000
$$

If you use `LaTex` to write scientific manuscripts, writing the formula above is relatively straightforwad. You can simply put something like this in your script.

```bash
$$
\delta^{13}C = \left( \frac{^{13}C/^{12}C_{sample}}{^{13}C/^{12}C_{standard}} - 1 \right) \times 1000
$$
```

However, for mere mortals like most of us, who have to deal with MS Word or Google Docs, adding those symbols is not trivial. In MS Words for instance, we could either use insert symbol or add customized keyboard shortcut to produce the symbols / notations on your documents. None of which feels intuitive nor easy.

After having to deal with the pain of inserting symbol and copy pasting notations on my word documents for many months at the start of my grad school, I found an easier, simpler, and more intuitive way to add geochemical notations on my word documents. It's so easy, I feel like describing it using words on this post won't do justice. So, instead I recorded a screen capture that you can see below.

<img src="{{ site.url }}{{ site.baseurl }}/assets/gifs/geochem.gif" alt="">

It looks easy, doesn't it? The method relies of MS Words autocorrect feature. So, if you are conviced that you want to hop on the "painless \\(\delta\\) notation on MS Word" train, you can follow these steps:

1. In MS Words, type in \\(\delta^{13}C\\) using insert symbol and superscript.

2. Select, and copy the \\(\delta^{13}C\\) you just typed.

3. Go to File

4. Click on `More...`

5. Click `Options`

6. Go to `Proofing`

7. Click `Auto Correct Options...`

8. You should see your \\(\delta^{13}C\\) in one the boxes.

9. Click `Formatted text`

10. Type in the characters that you want to automatically turn into \\(\delta^{13}C\\) in the box under `Replace`. For me it's d13c.

11. Click `Ok`

12. Try typing d13c and hit space or enter. It should automatically turn into \\(\delta^{13}C\\).

13. You can repeat the steps to add any kinds of notations you want. 

That's it. I hope this is helpful and let me know if you have questions.

Cheers,