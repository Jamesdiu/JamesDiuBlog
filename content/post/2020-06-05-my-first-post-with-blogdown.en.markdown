---
title: My first post with Blogdown (Small tips included!)
author: James Diu
date: '2020-06-05'
slug: my-first-post-with-blogdown
categories: []
tags:
  - blogdown
backtotop: no
toc: no
#draft: true
---

<!-- toc -->

Finally, I am starting my blog with `blogdown`! It takes a long time to establish it than I expected. Some Youtubes are now teaching how to build it within 15 min, which I have used much more than that! However, that is normal for someone like me without much experience with those Hugo, Netlify, and markdowns. To summerise all the steps I took, I would recommend reading the post from [Martin Frigaard](https://www.storybench.org/how-to-build-a-website-with-blogdown-in-r/).  This post provides very clear and informative descriptions of the same procedures I did. Indeed, it is also recommended to go through the book [Creating Website with R Markdown](https://bookdown.org/yihui/blogdown/workflow.html), written by YiHui Xie, so that to understand more about the tools and techniques used in `blogdown`. 

Meanwhile, I would like to add some tips that I noticed. I hope it may help with other beginners;

1. Pay attention to **HUGO VERSION** required of the template used. When you are connecting your blog to Netlify, you have to provide the "Environment variables" such that to ensure the version is update enough to run your template.  

2. Remember to change **baseURL** into the website address on netlify. Otherwise, it would not be able to obtain all data from git, for example the template design used.

3. Also for **baseURL**, make sure " / " is NOT missing at the end.

Above are the issues locked me down and took me few hours to find out the reason. Hope these would help the next one to go smoothly!














